# Go 并发编程

### 目录

- [一、并发与并行](#一并发与并行)
- [二、Goroutine](#二goroutine)
  - [2.1 Goroutine 是什么](#21-goroutine-是什么)
  - [2.2 Goroutine 的使用](22-Goroutine-的使用)
  - [2.3 Goroutine 和线程](#23-goroutine和线程)
- [三、Channel](#三channel)
  - [3.1 Channel 是什么](#31-channel-是什么)
  - [3.2 Channel 的使用](#32-channelc的使用)
  - [3.3 无缓存的 Channel](#33-无缓存的-channel)
  - [3.4 带缓存的 Channel](#34-带缓存的-channel)
  - [3.5 单方向的 Channel](#35-单方向的-channel)
- [四、select 多路复用](四select-多路复用)
- [五、基于共享变量的并发](#五基于共享变量的并发)
  - [5.1 sync.Mutex 互斥锁](#51-syncmutex-互斥锁)
  - [5.2 sync.RWMutex 读写锁](#52-syncrwmutex-读写锁)
  - [5.3 sync.WaitGroup](#53-syncwaitgroup)
  - [5.4 sync.Once](#54-synconce)
  - [5.5 sync.Map](#55-syncmap)
- [六、atomic包](#六atomic包)

---

## 一、并发与并行

并发：同一时间段内执行多个任务，强调的是在一段时间内执行多个任务。

并发：同一时刻内执行过个任务，强调的同一时刻执行多个任务。

## 二、Goroutine
### 2.1 Goroutine 是什么

在 Java 中要想实现并发编程，我们通常需要维护一个线程池，并且需要自己去实现线程接口或者类，才能完成线程的创建于管理，很大程度上提高了 Java 在并发中的难度。Go 语言是在多CPU、多核心的硬件时代才生的语言，语言本身对并发有着非常好的支持。

在Go语言中，每一个并发的执行单元叫作一个`goroutine`。`goroutine`类似于 Java 中的线程，属于用户态的线程，`goroutine`是由 Go 运行时（runtime）调度和管理。在运行时，`gorotine`中的任务会合理的分配给每个CPU及内核。Go语言之所以被称为现代化的编程语言，是因为它在语言层面已经内置了调度和上下文切换的机制。

在 Go 语言并发编程中不需要去自己实现线程、线程池，只需要一个`goroutine`，当你需要让某个任务并发执行的时候，只需将任务封装到函数中开一个`goroutine`就可以了。

### 2.2 Goroutine 的使用

Go 语言使用`goroutine`特别简单，在要并发执行的任务（必须是函数或匿名函数）前添加`go`关键字，程序运行到这行代码时，将会创建一个`goroute`去执行这个任务。

需要注意，main()函数所在`gorotine`为主`goroutine`，在这`goroutine`中创建的`goroutine`属于子`goroutine`，当主`goroutine`结束运行，其它的子`goroutine`也会退出运行。

```go
package main

import (
	"fmt"
	"sync"
)

var wg sync.WaitGroup	// 等待组，类似于 Java 中的 CountDownLatch

func sayHello() {
	fmt.Println("Hello Goroutine.")
	wg.Done()	// goroutine结束就登记-1
}

func main() {
	wg.Add(1)	// 启动一个goroutine就登记+1
	go sayHello()
	wg.Wait()	// 等待所有登记的 goroutine 运行结束
	fmt.Println("main goroutine done.")
}
```

### 2.3 Goroutine 和线程

上面我们已经知道如何使用 Goroutine，但是有人会好奇 Go 的 Goroutine 和操作系统线程有什么区别和关联。现在正让我们来看看两者的区别和连系。

 #### 动态栈

一个OS线程(操作系统线程)都有一个固定大小的内存块(一般会是2MB)来做栈，一个`goroutine`会以一个很小的栈开始其生命周期，一般只需要2KB。一个`goroutine`的栈，和操作系统线程一样，会保存其活跃或挂起的函数调用的本地变量，但是和OS线程不太一样的是一个`goroutine`的栈大小并不是固定的；栈的大小会根据需要动态地伸缩。而goroutine的栈的最大值有1GB，比传统的固定大小的线程栈要大得多。所以对于 GO 程序来说，同时创建成百上千个goroutine是非常普遍的。

#### Goroutine调度

在程序中一个`go`关键字即可实现并发编程，但是在语言底层具体实现比较复杂。Go 语言天然支持高并发的内在动力在于它的调度器。G，M，P 是调度器的三个核心组件，既 GMP 模型。

GMP模型中各个组件作用：

- G：取 goroutine 的首字母，主要保存 goroutine 的一些状态信息以及 CPU 的一些寄存器的值，例如 IP 寄存器，以便在轮到本 goroutine 执行时，CPU 知道要从哪一条指令处开始执行。 
- M：取 machine 的首字母，它代表一个工作线程，或者说系统线程。G 需要调度到 M 上才能运行，M 是真正工作的人。 
- P：取 processor 的首字母，为 M 的执行提供“上下文”，保存 M 执行 G 时的一些资源， 一个 M 只有绑定 P 才能执行 goroutine，当 M 被阻塞时，整个 P 会被传递给其他 M ，或者说整个 P 被接管。 

GPM 三足鼎力，共同成就 Go scheduler。G 需要在 M 上才能运行，M 依赖 P 提供的资源，P 则持有待运行的 G。

这个调度器还使用了一些技术手段，比如 m:n 调度，因为其会在 n 个操作系统线程上多工(调度) m 个 goroutine。Go 调度器的工作和内核的调度是相似的，但是这个调度器只关注单独的 Go 程序中的 goroutine。

#### GOMAXPROCS

Go的调度器使用了一个叫做GOMAXPROCS的变量来决定会有多少个操作系统的线程同时执行Go的代码。其默认的值是运行机器上的CPU的核心数，所以在一个有8个核心的机器上时，调度器一次会在8个OS线程上去调度GO代码。(GOMAXPROCS是前面说的m:n调度中的n)。在休眠中的或者在通信中被阻塞的goroutine是不需要一个对应的线程来做调度的。在I/O中或系统调用中或调用非Go语言函数时，是需要一个对应的操作系统线程的，但是GOMAXPROCS并不需要将这几种情况计算在内。

可以用GOMAXPROCS的环境变量来显式地控制这个参数，或者也可以在运行时用runtime.GOMAXPROCS函数来修改它。

 ## 三、Channel

### 3.1 Channel 是什么

如果说 goroutine 是 Go 语言程序的并发体的话，那么 channel 则是它们之间的通信机制。一个 channel 是一个通信机制，它可以让一个 goroutine 通过它给另一个 goroutine 发送值信息。

Go 语言中的通道（channel）是一种特殊的类型。 也就是channels可发送数据的类型。通道像一个传送带或者队列，总是遵循先入先出的规则，保证收发数据的顺序。每一个通道都是一个具体类型的导管，也就是声明channel的时候需要为其指定元素类型。 

### 3.2 Channel 的使用

#### 声明及初始化

channel 是一种类型，一种引用类型。声明方式：

```go
// 声明方式：var 变量名 chan 要传递元素的数据类型
var chInt chan int		// 声明一个传递整型的通道
var chBool chan bool	// 声明一个传递布尔型的通道
```

因为 channel 是一种引用类型，需要初始化，为初始化的变量值为 nil，初始化方式：

```go
// 初始化：make(chan 元素类型, [缓冲大小])
chInt = make(make int, 3)

// 可以只用简化的写法
ch2Int := make(make int, 3)
```

#### 操作

**将一个值发送到通道中：**

```go
ch <- 6 // 把6发送到 ch 中
```

 **从一个通道中接收值：**

```go
x := <- ch // 从 ch 中接收值并赋值给变量x
<-ch       // 从 ch 中接收值，忽略结果
```

**关闭通道：**

```go
// 用内置的 close 函数来关闭通道
close(ch)
```

关闭后的通道，向通道中发送数据将导致 panic；接收操作可以正常进行直到通道为空；如果通道中没有值了但仍然可以接收到对应类型的零值；关闭已经关闭的通道将导致panic。

注意：通道的操作只有`<-`操作符，没有`->`，切记；

**遍历通道：**

通道可以使用 for range 遍历，如果这个通道未关闭(未调用close方法)，这个循环将一直处于等待状态；如果这个通道是关闭的，for range 遍历完成后将会退出循环。

```go
func main() {
	ch := make(chan int)

	go func() {
		for i := 0; i < 100; i++ {
			ch <- i
		}
		close(ch)
	}()
	
	for i := range ch { // 通道关闭后会退出for range循环
		fmt.Println(i)
	}
}
```

### 3.3 无缓存的 Channel

无缓冲的通道又称为同步通道。我们来看一下下面的代码：

```go
func main() {
	ch := make(chan int)
	ch <- 6
	fmt.Println("发送成功")
}
```

上面这段代码能够通过编译，但是执行的时候会出现以下错误：

```bash
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan send]:
main.main()
	.../src/helloword/src/day_01/main.go:9 +0x5b
```

为什么会出现`deadlock`错误呢？

因为我们使用`ch := make(chan int)`创建的是无缓冲的通道，无缓冲的通道只有在有人接收值的时候才能发送值。就像你住的小区没有快递柜和代收点，快递员给你打电话必须要把这个物品送到你的手中，简单来说就是无缓冲的通道必须有接收才能发送。

上面的代码会阻塞在`ch <- 10`这一行代码形成死锁，那如何解决这个问题呢？

一种方法是启用一个`goroutine`去接收值，例如：

```go
func recv(c chan int) {
	ret := <-c
	fmt.Println("接收成功", ret)
}
func main() {
	ch := make(chan int)
	go recv(ch) // 启用goroutine从通道接收值
	ch <- 6
	fmt.Println("发送成功")
}
```

无缓冲通道上的发送操作会阻塞，直到另一个`goroutine`在该通道上执行接收操作，这时值才能发送成功，两个`goroutine`将继续执行。相反，如果接收操作先执行，接收方的goroutine将阻塞，直到另一个`goroutine`在该通道上发送一个值。

使用无缓冲通道进行通信将导致发送和接收的`goroutine`同步化。因此，无缓冲通道也被称为`同步通道`。Java 语言开发者直到，在 Java 中可以使用等待通知(wait/notify)来进行两个线程之间的同步。在 Go 语言中可以使用无缓存通道实现这一功能。

### 3.4 带缓存的 Channel

带缓存的 Channel 内部持有一个元素队列。队列的最大容量是在调用 make 函数创建 channel 时通过第二个参数指定的。下面的语句创建了一个可以持有三个字符串元素的带缓存Channel。

```Go
ch = make(chan string, 3)
```

在通道不满和不空的情况下，调用的线程不会发生阻塞的现象。

### 3.5 单方向的 Channel

有的时候我们会将通道作为参数在多个函数间传递，很多时候我们在不同的任务函数中使用通道都会对其进行限制，比如限制通道在函数中只能发送或只能接收，以保证通道的安全。

Go语言中提供了**单向通道** 来处理满足这种需求，需要注意双向通道可以转换为单向通道，但是单向通道不能转换成双向通道：

```go
func counter(out chan<- int) {
    for x := 0; x < 100; x++ {
        out <- x
    }
    close(out)
}

func squarer(out chan<- int, in <-chan int) {
    for v := range in {
        out <- v * v
    }
    close(out)
}

func printer(in <-chan int) {
    for v := range in {
        fmt.Println(v)
    }
}

func main() {
    naturals := make(chan int)
    squares := make(chan int)
    go counter(naturals)
    go squarer(squares, naturals)
    printer(squares)
}
```

其中（单向通道的声明），

- `chan<- int`是一个只写单向通道，可以对其执行发送操作但是不能执行接收操作；
- `<-chan int`是一个只读单向通道，可以对其执行接收操作但是不能执行发送操作。

## 四、select 多路复用

select  可以从多个通道接收值，根据接收的值不同，选择不同的通道。

`select`的使用类似于switch语句，它有一系列case分支和一个默认的分支。每个case会对应一个通道的通信（接收或发送）过程。`select`会一直等待，直到某个`case`的通信操作完成时，就会执行`case`分支对应的语句。格式如下： 

```go
select {
case <-ch1:
    // ...
case x := <-ch2:
    // ...use x...
case ch3 <- y:
    // ...
default:
    // ...
}
```

select 的特点：

- 可处理一个或多个channel的发送/接收操作。
- 如果多个`case`同时满足，`select`会随机选择一个。
- 对于没有`case`的`select{}`会一直等待，可用于阻塞main函数。



Example：

```go
func main() {
	ch := make(chan int, 1)
	for i := 0; i < 10; i++ {
		select {
		case x := <-ch:
			fmt.Println(x)
		case ch <- i:
		}
	}
}
```

## 五、基于共享变量的并发

### 5.1 sync.Mutex 互斥锁

 互斥锁是一种常用的控制共享资源访问的方法，它是一种悲观锁，适合多写场景，同一时刻，只允许一个 goroutine 访问加锁的资源。 Go语言中使用`sync`包的`Mutex`类型来实现互斥锁，加锁使用 `sync.Mutex.Lock()`，释放锁使用`sync.Mutex.Unlock()`。

```go
var (
	lock    sync.Mutex
	sum int
	wg sync.WaitGroup
)

func add(a int) {
	lock.Lock()
	sum += a
	lock.Unlock()

	wg.Done()
}

func main() {
	wg.Add(10)
	for i := 0; i < 10; i++ {
		go add(i)
	}
	wg.Wait()
	fmt.Println(sum)
}
```

### 5.2 sync.RWMutex 读写锁

互斥锁由于操作临界资源之前无论是读还是写都会进行加锁操作，有很多实际的场景下是读多写少的，当我们并发的去读取一个资源不涉及资源修改的时候是没有必要加锁的，这种场景下使用读写锁是更好的一种选择。读写锁在Go语言中使用`sync`包中的`RWMutex`类型。

读写锁分为两种：读锁和写锁。当一个goroutine获取读锁之后，其他的`goroutine`如果是获取读锁会继续获得锁，如果是获取写锁就会等待；当一个`goroutine`获取写锁之后，其他的`goroutine`无论是获取读锁还是写锁都会等待。

```go
// 读锁使用方式
var mu sync.RWMutex
var balance int
func Balance() int {
    mu.RLock() // readers lock
    defer mu.RUnlock()
    return balance
}

// 写锁的使用同互斥锁
```

### 5.3 sync.WaitGroup

`sync.WaitGroup`用于阻塞主线程的执行，等到所有的goroutine执行完成。它内部维护着一个计数器，当我们启动了N 个并发任务时，就将计数器值增加N。每个任务完成时通过调用Done()方法将计数器减1。通过调用Wait()来等待并发任务执行完，当计数器值为0时，因调用Wait()方法而阻塞的线程开始继续执行。 

```go
var wg sync.WaitGroup

func hello() {
	defer wg.Done()
	fmt.Println("Hello Goroutine!")
}

func main() {
	wg.Add(1)
	go hello()
	fmt.Println("main goroutine done!")
	wg.Wait()
}
```

### 5.4 sync.Once

在并发编程的很多场景下我们需要确保某些操作在高并发的场景下只执行一次，例如只加载一次配置文件、只关闭一次通道等。Go语言中的`sync`包中提供了一个针对只执行一次场景的解决方案–`sync.Once`。

`sync.Once`只有一个`Do`方法，通过这个方法调用的方法，只会被执行一次。

```go
var loadIconsOnce sync.Once
var icons map[string]image.Image

func loadIcons() {
    icons = map[string]image.Image{
        "spades.png":   loadIcon("spades.png"),
        "hearts.png":   loadIcon("hearts.png"),
        "diamonds.png": loadIcon("diamonds.png"),
        "clubs.png":    loadIcon("clubs.png"),
    }
}

// Concurrency-safe.
func Icon(name string) image.Image {
    loadIconsOnce.Do(loadIcons)
    return icons[name]
}
```

### 5.5 sync.Map

Go 语言中内置的 map 不是并发安全的，并发场景下容易造成数据安全性问题。为了弥补这一问题，在 `sync`包中提供了一个开箱即用的并发安全版map–`sync.Map`。开箱即用表示不用像内置的map一样使用make函数初始化就能直接使用。同时`sync.Map`内置了诸如`Store`、`Load`、`LoadOrStore`、`Delete`、`Range`等操作方法。

```go
var m = sync.Map{}

func main() {
	wg := sync.WaitGroup{}
	for i := 0; i < 20; i++ {
		wg.Add(1)
		go func(n int) {
			key := strconv.Itoa(n)
			m.Store(key, n)
			value, _ := m.Load(key)
			fmt.Printf("k=:%v,v:=%v\n", key, value)
			wg.Done()
		}(i)
	}
	wg.Wait()
}
```

 ## 六、atomic包

这个包与 Java 语言的原子包出发点和目的相同，使用场景和用法也是相同的。

| 方法                                                         |   解释   |
| :----------------------------------------------------------- | :------: |
| func LoadInt32(addr *int32) (val int32)<br/>func LoadInt64(addr *int64) (val int64) <br/>func LoadUint32(addr *uint32) (val uint32) <br/>func LoadUint64(addr *uint64) (val uint64) <br/>func LoadUintptr(addr *uintptr) (val uintptr) <br/>func LoadPointer(addr *unsafe.Pointer) (val unsafe.Pointer) | 读取操作 |
| func StoreInt32(addr *int32, val int32) <br/>func StoreInt64(addr *int64, val int64) <br/>func StoreUint32(addr *uint32, val uint32) <br/>func StoreUint64(addr *uint64, val uint64) <br/>func StoreUintptr(addr *uintptr, val uintptr) <br/>func StorePointer(addr *unsafe.Pointer, val unsafe.Pointer) | 写入操作 |
| func AddInt32(addr *int32, delta int32) (new int32) <br/>func AddInt64(addr *int64, delta int64) (new int64) <br/>func AddUint32(addr *uint32, delta uint32) (new uint32) <br/>func AddUint64(addr *uint64, delta uint64) (new uint64) <br/>func AddUintptr(addr *uintptr, delta uintptr) (new uintptr) | 修改操作 |
| func SwapInt32(addr *int32, new int32) (old int32) <br/>func SwapInt64(addr *int64, new int64) (old int64) <br/>func SwapUint32(addr *uint32, new uint32) (old uint32) <br/>func SwapUint64(addr *uint64, new uint64) (old uint64) <br/>func SwapUintptr(addr *uintptr, new uintptr) (old uintptr) <br/>func SwapPointer(addr *unsafe.Pointer, new unsafe.Pointer) (old unsafe.Pointer) | 交换操作 |
| func CompareAndSwapInt32(addr *int32, old, new int32) (swapped bool) <br/>func CompareAndSwapInt64(addr *int64, old, new int64) (swapped bool) <br/>func CompareAndSwapUint32(addr *uint32, old, new uint32) (swapped bool) <br/>func CompareAndSwapUint64(addr *uint64, old, new uint64) (swapped bool) <br/>func CompareAndSwapUintptr(addr *uintptr, old, new uintptr) (swapped bool) <br/>func CompareAndSwapPointer(addr *unsafe.Pointer, old, new unsafe.Pointer) (swapped bool) |  比较并  |



### License

MIT License GoodCoder-Notes，转载请注明出处。