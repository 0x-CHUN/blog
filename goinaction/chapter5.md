# Go语言的类型系统

Go语言是一种静态类型的编程语言。

## 用户定义的类型

声明用户定义的类型的两个方法：

* struct

  ```go
  type user struct {
  	name       string
  	email      string
  	ext        int
  	privileged bool
  }
  ```

  也可以嵌套：

  ```go
  type admin struct {
  	person user
  	level  string
  }
  ```

* 基于一个已有的类型，将其作为新类型的类型说明

  ```go
  type Duration int64 
  ```

  这是是标准库的`time`包里的一个类型的声明。虽然`int64`是基础类型，Go并不认为`Duration`和`int64`是同一种类型。这两个类型是完全不同的有区别的类型。

## 方法

方法能给用户定义的类型添加新的行为。方法实际上也是函数，只是在声明时，在关键字`func`和方法名之间增加了一个参数:

```go
type user struct {
	name string
	email string
}
//两个方法
func (u user)notify()  {
	fmt.Printf("Sending User Email To %s<%s>\n",u.name,u.email)
}

func (u *user)changeEmail(email string)  {
	u.email =email
}
```

Go语言里有两种类型的接收者：**值接收者**和**指针接收者**。

## 类型的本质

内置类型：数值类型、字符串类型和布尔类型。

引用类型：切片、映射、通道、接口和函数类型。

结构类型：用来描述一组数据值，这组值的本质即可以是原始的，也可以是非原始的。

## 接口

多态是指代码可以根据类型的具体实现采取不同行为的能力。如果一个类型实现了某个接口，所有使用这个接口的地方，都可以支持这种类型的值。

接口是用来定义行为的类型。这些被定义的行为不由接口直接实现，而是通过方法由用户定义的类型实现。如果用户定义的类型实现了某个接口类型声明的一组方法，那么这个用户定义的类型的值就可以赋给这个接口类型的值。这个赋值会把用户定义的类型的值存入接口类型的值。

对接口值方法的调用会执行接口值里存储的用户定义的类型的值对应的方法。因为任何用户定义的类型都可以实现任何接口，所以对接口值方法的调用自然就是一种多态。在这个关系里，用户定义的类型通常叫作**实体类型**，原因是如果离开内部存储的用户定义的类型的值的实现，接口值并没有具体的行为。

方法集定义了一组关联到给定类型的值或者指针的方法。定义方法时使用的接收者的类型决定了这个方法是关联到值，还是关联到指针，还是两个都关联。

Go语言规范里定义的方法集的规则

```
Values　　　　　　　　Methods Receivers
-----------------------------------------------
　　T　　　　　　　　　　(t T)
　 *T　　　　　　　　　　(t T) and (t *T)
```

`T`类型的值的方法集只包含值接收者声明的方法。而指向`T`类型的指针的方法集既包含值接收者声明的方法，也包含指针接收者声明的方法。

多态：

```go
package main

import "fmt"

type notifier interface {
	notify()
}

type user struct {
	name  string
	email string
}

func (u *user) notify() {
	fmt.Printf("Sending user email to %s<%s>\n", u.name, u.email)
}

type admin struct {
	name  string
	email string
}

func (a *admin) notify()  {
	fmt.Printf("Sending admin email to %s<%s>\n",a.name,a.email)
}

func main() {
	bill := user{"Bill", "bill@email.com"}
	sendNotification(&bill)

	lisa := admin{"Lisa", "lisa@email.com"}
	sendNotification(&lisa)
}

func sendNotification(n notifier)  {
	n.notify()
}
```

多态函数`sendNotification`，这个函数接受一个实现了`notifier`接口的值作为参数。既然任意一个实体类型都能实现该接口，那么这个函数可以针对任意实体类型的值来执行`notifier`方法。因此，这个函数就能提供多态的行为。

## 嵌入类型

```go
package main

import "fmt"

type notifier interface {
	notify()
}

type user struct {
	name  string
	email string
}

func (u *user) notify() {
	fmt.Printf("Sending user email to %s<%s>\n", u.name, u.email)
}

type admin struct {
	user //嵌入类型
	level string
}

func (a *admin)notify()  {
	fmt.Printf("Sending admin email to %s<%s>\n", a.name, a.email)
}

func main() {
	ad := admin{
		user: user{
			name:  "john smith",
			email: "john@yahoo.com",
		},
		level: "super",
	}

	sendNotification(&ad)// 内部类型的方法没有被提升
	ad.user.notify()//内部类型的实现
	ad.notify()// 内部类型的方法没有被提升
}

func sendNotification(n notifier)  {
	n.notify()
}
```

## 公开或未公开的标识符

使用小写字母声明的，所以这个标识符是未公开的。

使用大写字母声明的，所以这个标识符是公开的

不过，即便内部类型是未公开的，内部类型里声明的字段依旧是公开的。既然内部类型的标识符提升到了外部类型，这些公开的字段也可以通过外部类型的字段的值来访问。