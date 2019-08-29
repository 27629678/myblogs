> 摘要：到目前为止，个人认为`golang`是最简单的可用于生产环境编程语言之一，强类型检查，有GC但无虚拟机，从语言层面解决了OO（Object-Orientied）编程思想的问题，并且还简化了并发编程模型，极大地提供了开发效率；

## 1. struct

与C/C++中的结构体类似，但`go`中所有的类型都有隐式的`prototype`为`interface{}`，与其它`OOP`语言的`meta-class`不同，该原型无任何可用的`field`和`method`；

```
type Animal struct {
	name string
}

// a := new(Animal)
// a := Animal{name: "max"}
a := Animal {
	name: "max",	// 后面的逗号不能省略
}
a = "max"
```

## 2. inheritance

`go`并没有严格意义上的继承概念，而是使用了组合，在`go`中叫`embeding type`，如下所示：

```
type Dog struct {
	// dog Dog，可写作下面的形式
	Dog
	string category
}

dog := new(Dog)
dog.name = "max"
dog.category = "c1"
```

> NOTE: `go`中无`Polymorphism`的概念，此处不再多说

## 3. interface

`interface`定义形式如下，与Java不同，`struct`不会显式的声明实现了某接口


```
type Handler interface {
	handle(msg interface{})
}

func (d *Dog) handle(msg string) {
	fmt.Println(d.name)
}

// 编译器保证Dog实现了Handler接口
var handler Handler = dog
handler.handle("hello")
```
## 4. access control

`访问控制`也是隐式的，不像C++/Java/Swift等语言，使用file, public, private, protected等等约束Class，Property和Function的可见性；

```
type Dog struct {}	// 包外可见，对外提供类型
type dog struct {}	// 包内可见，隐藏实现细节

func Foo() {}		// 包外可见，对外提供方法
func foo() {}		// 包内可见，隐藏实现细节
```

## 5. concurrency

引入了`协程`的概念，无`进程`和`线程`的概念，使得**并发编程**模型更加简化，与C#的协程类似，并在此基础上提供了`channel`实现`协程`数据同步，概念相对简单不再赘述；

## 6. package and package manager

`go`提供了包管理器，用于打包和解决包依赖的问题，大大提升了开发效率和解决了代码复用的问题；