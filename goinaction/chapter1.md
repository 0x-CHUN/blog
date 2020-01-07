# 关于Go语言的介绍

## 用Go解决现代编程难题

* Go编译速度快
* 内置并发设计
* 自带垃圾回收

### 并发

Go语言对并发的支持是这门语言最重要的特性之一。

goroutine很像线程，但是它占用的内存远少于线程，使用它需要的代码更少。通道（channel）是一种内置的数据结构，可以让用户在不同的goroutine之间同步发送具有类型的消息。

1. goroutine

   协程，goroutine使用的内存比线程更少。

2. 通道

   一种数据结构，用于goroutine之间共享内存的访问。

### 类型系统

独特的接口实现机制。

```go
interface User{
    public void login();
    public void logout();
}
```

接口用于描述类型的行为。