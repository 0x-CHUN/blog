# 并发模式

## runner

`runner`包用于展示如何使用通道来监视程序的执行时间，如果程序运行时间太长，也可以用`runner`包来终止程序。当开发需要调度后台处理任务的程序的时候，这种模式会很有用。这个程序可能会作为cron作业执行，或者在基于定时任务的云环境（如iron.io）里执行。

```go
package runner

import (
	"errors"
	"os"
	"os/signal"
	"time"
)

type Runner struct {
	// interrupt通道报告从操作系统发送的信号
	interrupt chan os.Signal
	// complete通道报告处理任务已经完成
	complete chan error
	// timeout报告处理任务已经超时
	timeout <-chan time.Time
	// tasks持有一组以索引顺序依次执行的函数
	tasks []func(int)
}

// ErrTimeout会在任务执行超时时返回
var ErrTimeout = errors.New("received timeout")

// ErrInterrupt会在接收到操作系统的事件时返回
var ErrInterrupt = errors.New("received interrupt")

// New返回一个新的准备使用的Runner
func New(d time.Duration) *Runner {
	return &Runner{
		interrupt: make(chan os.Signal, 1),
		complete:  make(chan error),
		timeout:   time.After(d),
	}
}

// Add将一个任务附加到Runner上。这个任务是一个
func (r *Runner) Add(tasks ...func(int)) {
	r.tasks = append(r.tasks, tasks...)
}

func (r *Runner) Start() error {
	signal.Notify(r.interrupt, os.Interrupt)
	go func() {
		r.complete <- r.run()
	}()
	select {
	// 当任务处理完成时发出的信号
	case err := <-r.complete:
		return err
	// 当任务处理程序运行超时时发出的信号
	case <-r.timeout:
		return ErrTimeout
	}
}

// run执行每一个已注册的任务
func (r *Runner) run() error {
	for id, task := range r.tasks {
		if r.gotInterrupt() {
			return ErrInterrupt
		}
		task(id)
	}
	return nil
}

func (r *Runner) gotInterrupt() bool {
	select {
	// 当中断事件被触发时发出的信号
	case <-r.interrupt:
		// 停止接收后续的任何信号
		signal.Stop(r.interrupt)
		return true
	default: // 继续正常运行
		return false
	}
}
```

## pool

使用有缓冲的通道实现资源池，来管理可以在任意数量的goroutine之间共享及独立使用的资源。这种模式在需要共享一组静态资源的情况（如共享数据库连接或者内存缓冲区）下非常有用。如果goroutine需要从池里得到这些资源中的一个，它可以从池里申请，使用完后归还到资源池里。

```go
package pool

import (
	"errors"
	"io"
	"log"
	"sync"
)

// Pool管理一组可以安全地在多个goroutine间共享的资源。被管理的资源必须实现io.Closer接口
type Pool struct {
	m         sync.Mutex
	resources chan io.Closer
	factory   func() (io.Closer, error)
	closed    bool
}

// ErrPoolClosed表示请求（Acquire）了一个已经关闭的池
var ErrPoolClosed = errors.New("Pool has been closed.")

// New创建一个用来管理资源的池。
// 这个池需要一个可以分配新资源的函数，
// 并规定池的大小
func New(fn func() (io.Closer, error), size uint) (*Pool, error) {
	if size <= 0 {
		return nil, errors.New("Size value too small.")
	}
	return &Pool{
		factory:   fn,
		resources: make(chan io.Closer, size),
	}, nil
}

// Acquire从池中获取一个资源
func (p *Pool) Acquire() (io.Closer, error) {
	select {
	case r, ok := <-p.resources:
		log.Println("Acquire:", "Shared Resource")
		if !ok {
			return nil, ErrPoolClosed
		}
		return r, nil
	default:
		log.Println("Acquire:", "New Resource")
		return p.factory()
	}
}

// Release将一个使用后的资源放回池里
func (p *Pool) Release(r io.Closer) {
	p.m.Lock()
	defer p.m.Unlock()

	if p.closed {
		r.Close()
		return
	}
	select {
	case p.resources <- r:
		log.Println("Release:", "In Queue")
	default:
		log.Println("Release:", "Closing")
		r.Close()
	}
}

// Close会让资源池停止工作，并关闭所有现有的资源
func (p *Pool) Close() {
	// 保证本操作与Release操作的安全
	p.m.Lock()
	defer p.m.Unlock()

	// 如果pool已经被关闭，什么也不做
	if p.closed {
		return
	}
	// 将池关闭
	p.closed = true
	// 在清空通道里的资源之前，将通道关闭
	// 如果不这样做，会发生死锁
	close(p.resources)
	for r := range p.resources{
		r.Close()
	}
}
```

## work

使用无缓冲的通道来创建一个goroutine池，这些goroutine执行并控制一组工作，让其并发执行。在这种情况下，使用无缓冲的通道要比随意指定一个缓冲区大小的有缓冲的通道好，因为这个情况下既不需要一个工作队列，也不需要一组goroutine配合执行。无缓冲的通道保证两个goroutine之间的数据交换。这种使用无缓冲的通道的方法允许使用者知道什么时候goroutine池正在执行工作，而且如果池里的所有goroutine都忙，无法接受新的工作的时候，也能及时通过通道来通知调用者。使用无缓冲的通道不会有工作在队列里丢失或者卡住，所有工作都会被处理。

```go
package work

import "sync"

// Worker必须满足接口类型，
type Worker interface {
	Task()
}

type Pool struct {
	work chan Worker
	wg   sync.WaitGroup
}

func New(maxGoroutines int) *Pool {
	p := Pool{
		work: make(chan Worker),
	}
	p.wg.Add(maxGoroutines)
	for i := 0; i < maxGoroutines; i++ {
		go func() {
			for w := range p.work {
				w.Task()
			}
			p.wg.Done()
		}()
	}
	return &p
}

func (p *Pool) Run(w Worker) {
	p.work <- w
}

func (p *Pool) Shutdown() {
	close(p.work)
	p.wg.Wait()
}
```

