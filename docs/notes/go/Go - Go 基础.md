# Go 基础

### 目录

- [一、程序结构](#一程序结构)
  - [1.1 Hello，World](#11-helloworld)
  - [1.2 包](#12-包)
  - [1.3 声明](#13-声明)
  - [1.4 数据类型](#14-数据类型)
  - [1.5 关键字](#15-关键字)
  - [1.6 逻辑控制](#16-逻辑控制)
- [二、函数](#二函数)
  - [2.1 函数声明](#21-函数声明)
  - [2.2 可变参数](#22-可变参数)
  - [2.3 Deferred函数](#23-deferred函数)
  - [2.4 异常处理](#24-异常处理)
  - [2.5 函数值](#25-函数值)
- [三、方法](#三方法)
  - [3.1 方法声明](#31-方法声明)
  - [3.2 嵌入式结构体](#32-嵌入式结构体可以理解为组合或继承)
  - [3.3 方法值与方法表达式](#33-方法值与方法表达式)
- [四、封装](#四封装)
  - [4.1 函数/方法封装](#41-函数方法封装)
  - [4.2 变量封装](#42-变量封装)
- [五、接口](#五接口)
  - [5.1 接口定义](#51-接口定义)
  - [5.2 接口嵌套](#52-接口嵌套)
  - [5.3 空接口](#53-空接口)
  - [5.4 类型断言](#54-类型断言)
- [六、反射](#六反射)
  - [6.1 reflect.TypeOf](#61-reflecttypeof)
  - [6.2 reflect.ValueOf](#62-reflectvalueof)
  - [6.3 反射的利弊](#63-反射的利弊)

---

## 一、程序结构

### 1.1 Hello，World

Go 语言环境变量配置网上教程很多，不在此赘述。

我们将用传统的 “Hello World” 案例开始讲述。每个 Go 文件必须包含包名（package XXX）。一个独立可执行的 Go 程序要满足两个条件：包名为 main、程序中有 main 函数； main 包比较特殊，它定义了一个独立可执行的程序，而不是一个库。在 main 里的 main 函数也很特殊，它是整个程序执行时的入口 。

```go
package main // 包名

import "fmt" // 导入包

// 声明全局变量

// 声明包初始化函数init()，一个包可以有多个

// 声明函数、声明方法

func main() {	// 程序入口
	fmt.Println("Hello,World")
}
```

使用 Go 语言提供的 run 命令，运行程序：

```go
go run helloworld.go
```

### 1.2 包

 Go语言的代码通过`包（package）`组织，包类似于其它语言里的库（libraries）或者模块（modules）。一个包由位于单个目录下的一个或多个.go源代码文件组成, 目录定义包的作用。每个源文件都以一条`package`声明语句开始，这个例子里就是`package main`, 表示该文件属于哪个包，紧跟着一系列导入（import）的包，之后是存储在这个文件里的程序语句。 

包声明：

```
package XXX
```

单个导入包：

```go
import "fmt"
```

多个导入包：

```
import (
    "fmt"
    "os"
)
```

匿名包导入：

```
import _ "fmt"
```

### 1.3 声明

 Go语言主要有四种类型的声明语句：var、const、type和func，分别对应**变量**、**常量**、**类型**和**函数实体对象**的声明。

#### 变量 声明有一下几种方式：

```go
// 1. 普通声明方式
// var 变量名 类型 = 表达式    PS：当缺省“= 表达式”时，使用该类型的默认值
var s string                    // 此时s = ""，因为string类型的默认值为“”，也就是空字符串
var s string = "hello world"

var s = "hello world"           // 类型也可以省略，类型由初始化表达式推导
var i, j, k int                 // int, int, int
var b, f, s = true, 2.3, "four" // bool, float64, string

// 2. 简短变量声明（只能用于局部变量声明）
// 在函数内部，可以“名字 := 表达式”形式声明变量，变量的类型根据表达式来自动推导
i := 100                        // int
i, j := 0, 1					// int, int

// 3. 指针
x := 1
p := &x         				// p, of type *int, points to x

// 3. New函数
// 表达式new(T)将创建一个T类型的匿名变量，初始化为T类型的零值，然后返回变量地址，返回的指针类型为*T
p := new(int)   				// p, *int 类型, 指向匿名的 int 变量
fmt.Println(*p) 				// "0"
*p = 2          				// 设置 int 匿名变量的值为 2
fmt.Println(*p)					// "2"

// PS：当变量为全局变量，且有多个时，可采用以下的声明方式，批量声明多个变量
var (
	i = 1
	s = "string"
	b = true
)
```

#### 常量 声明方式

```go
// 一个常量的声明语句
const pi = 3.14159

// 批量声明多个常量
const (
    e  = 2.71828182845904523536028747135266249775724709369995957496696763
    pi = 3.14159265358979323846264338327950288419716939937510582097494459
)
```

#### 类型 声明方式

```go
// 一个类型声明语句创建了一个新的类型名称，和现有类型具有相同的底层结构。
// 新命名的类型提供了一个方法，用来分隔不同概念的类型，这样即使它们底层类型相同也是不兼容的。
// 类型声明语句一般出现在包一级
// type 类型名字 底层类型
```

#### 函数 声明方式

``` go
// 粗略讲解，后续会有详细的讲解
func search() {
    fmt.println("search func")
}
```

### 1.4 数据类型

 Go语言将数据类型分为四类：基础类型、复合类型、引用类型和接口类型。 

#### 基础类型：

- 数值类型：
  - 整数：
    - 字节：byte
    -  特定CPU平台 机器字大小的有符号和无符号整数：int 和 uint 
    - 有符号整数： int8、int16、int32 和 int64
    - 无符号整数： uint8、uint16、uint32 和 uint64 
    -  Unicode字符：rune（int32等价）
    -  无符号的整数类型： uintptr（没有指定具体的bit大小但是足以容纳指针）
  - 浮点数： float32 和 float64 
  - 复数： complex64 和 complex128 
- 布尔类型：bool（值只有两种：true 和 false）
- 字符串：string
- 常量 ：const

#### 复合类型：

`数组`是一个由固定长度的特定类型元素组成的序列，一个数组可以由零个或多个元素组成； 因为数组的长度是固定的，因此在Go语言中很少直接使用数组。

`结构体`是一种聚合的数据类型，是由零个或多个任意类型的值聚合成的实体。每个值称为结构体的成员。 

#### 引用类型：

`指针` 保存了所指向变量对应类型值的内存空间。

`Slice（切片）`代表变长的序列，序列中每个元素都有相同的类型。一个slice类型一般写作[]T，其中T代表slice中元素的类型；slice的语法和数组很像，只是没有固定长度而已。

`Map（字典）`它是一个无序的key/value对的集合，其中所有的key都是不同的，然后通过给定的key可以在常数时间复杂度内检索、更新或删除对应的value。 

`函数`在Go语言中，函数可以当做变量一样进行传递。

`通道`channels是它们之间的通信机制，声明方式 ch := make(chan int) // ch has type 'chan int'

#### 接口类型：

`接口类型`具体描述了一系列方法的集合，一个实现了这些方法的具体类型是这个接口类型的实例。interface{}空接口是所有数据类型的父类型，类似于Java中的Object类。

### 1.5 关键字

Go语言中关键字有25个；关键字不能用于自定义名字，只能在特定语法结构中使用。

```go
break      default       func     interface   select
case       defer         go       map         struct
chan       else          goto     package     switch
const      fallthrough   if       range       type
continue   for           import   return      var
```

此外，还有大约30多个预定义的名字，比如int和true等，主要对应内建的常量、类型和函数。

```go
内建常量: true false iota nil

内建类型: int int8 int16 int32 int64
         uint uint8 uint16 uint32 uint64 uintptr
         float32 float64 complex128 complex64
         bool byte rune string error

内建函数: make len cap new append copy close delete
         complex real imag
         panic recover
```

这些内部预先定义的名字并不是关键字，你可以在定义中重新使用它们。在一些特殊的场景中重新定义它们也是有意义的，但是也要注意避免过度而引起语义混乱。 


### 1.6 逻辑控制

#### if 语句

```go
if 布尔表达式 {
   /* 在布尔表达式为 true 时执行 */
}

if 布尔表达式 {
   /* 在布尔表达式为 true 时执行 */
} else {
  /* 在布尔表达式为 false 时执行 */
}
```

#### for 循环

```go
for init; condition; post {}

// 其它语言的while(bool)
for condition {}

// 其它语言的for(;;)
for {}

PS：break、contiune、goto均支持
```



####  switch  语句

```go
switch var1 {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
```

#### select 语句

```go
select {
    case communication clause  :
       statement(s);      
    case communication clause  :
       statement(s);
    /* 你可以定义任意数量的 case */
    default : /* 可选 */
       statement(s);
}
```

## 二、函数
### 2.1 函数声明

#### 具名函数

函数声明包括函数名、形式参数列表、返回值列表（可省略）以及函数体。 

```go
func function_name( [parameter list] ) [return_types] {
   函数体
}
```

Example：

```go
func add(a int, b int) int {
    return a + b
}

func add(a, b int) (ret int) {
    ret = a + b
	return
}

func search() (int, int) {
    return 1, 2
}
```

形式参数类型相同时，可以省略其它的类型，在最后只留一个类型；函数支持单返回值，也支持多返回值；相比于其它单返回值的语言，多返回值提供了很多便利。

#### 匿名函数

匿名函数的语法和函数声明相似，区别在于func关键字后没有函数名。

Example：

```go
go func(i int) {
    fmt.println(i)
}(10) // 输出 10
```

### 2.2 可变参数

参数数量可变的函数称为为可变参数函数。典型的例子就是fmt.Println函数。 

在声明可变参数函数时，需要在参数列表的最后一个参数类型之前加上省略符号“...”，这表示该函数会接收任意数量的该类型参数。 

Example：

```go
func sum(vals...int) int {
    total := 0
    for _, val := range vals {
        total += val
    }
    return total
}
```

### 2.3 Deferred函数

延迟执行语句，通过defer关键字声明。加上defer关键字的函数或方法，在defer语句被执行时，跟在defer后面的函数会被延迟执行。直到包含该defer语句的函数执行完毕时，defer后的函数才会被执行，不论包含defer语句的函数是通过return正常结束，还是由于panic导致的异常结束。你可以在一个函数中执行多条defer语句，它们的执行顺序与声明顺序相反。 

 defer语句经常被用于处理成对的操作，如打开、关闭、连接、断开连接、加锁、释放锁。通过defer机制，不论函数逻辑多复杂，都能保证在任何执行路径下，资源被释放。 

```go
func ReadFile(filename string) ([]byte, error) {
    f, err := os.Open(filename)
    if err != nil {
        return nil, err
    }
    defer f.Close()
    return ReadAll(f)
}
```

### 2.4 异常处理

#### 错误处理策略

1. 最常用的方式是传播错误。这意味着函数中某个子程序的失败，会变成该函数的失败。 

```go
resp, err := http.Get(url)
if err != nil{
    return nil, err
}
```

2. 重新尝试失败的操作。如果错误的发生是偶然性的，或由不可预知的问题导致的。一个明智的选择是重新尝试失败的操作。在重试时，我们需要限制重试的时间间隔或重试的次数，防止无限制的重试。 

```go
func WaitForServer(url string) error {
    const timeout = 1 * time.Minute
    deadline := time.Now().Add(timeout)
    for tries := 0; time.Now().Before(deadline); tries++ {
        _, err := http.Head(url)
        if err == nil {
            return nil // success
        }
        log.Printf("server not responding (%s);retrying…", err)
        time.Sleep(time.Second << uint(tries)) // exponential back-off
    }
    return fmt.Errorf("server %s failed to respond after %s", url, timeout)
}
```

3.  输出错误信息并结束程序。

```go
if err := WaitForServer(url); err != nil {
    fmt.Fprintf(os.Stderr, "Site is down: %v\n", err)
    os.Exit(1)
}
// 或
if err := WaitForServer(url); err != nil {
    log.Fatalf("Site is down: %v\n", err)
}
```

4. 输出错误信息就足够了，不需要中断程序的运行。 

```go
if err := Ping(); err != nil {
    log.Printf("ping failed: %v; networking disabled",err)
}
```

5. 直接忽略掉错误。

```go
// ...use temp dir…
os.RemoveAll(dir) // ignore errors;
```

#### Panic异常及Recover异常捕获

Go的类型系统会在编译时捕获很多错误，但有些错误只能在运行时检查，如数组访问越界、空指针引用等。这些运行时错误会引起painc异常。 一般而言，当panic异常发生时，程序会中断运行。

通常来说，不应该对panic异常做任何处理，但有时，也许我们可以从异常中恢复，至少我们可以在程序崩溃前，做一些操作。 

如果在deferred函数中调用了内置函数recover，并且定义该defer语句的函数发生了panic异常，recover会使程序从panic中恢复，并返回panic value。导致panic异常的函数不会继续运行，但能正常返回。在未发生panic时调用recover，recover会返回nil。 

```go
func Parse(input string) (s *Syntax, err error) {
    defer func() {
        if p := recover(); p != nil {
            err = fmt.Errorf("internal error: %v", p)
        }
    }()
    // ...parser...
}
```

### 2.5 函数值

在Go中，函数被看作第一类值（first-class values）：函数像其他值一样，拥有类型，可以被赋值给其他变量，传递给函数，从函数返回。对函数值（function value）的调用类似函数调用。

```Go
func square(n int) int { return n * n }
func negative(n int) int { return -n }
func product(m, n int) int { return m * n }

f := square
fmt.Println(f(3)) // "9"

f = negative
fmt.Println(f(3))     // "-3"
fmt.Printf("%T\n", f) // "func(int) int"

f = product // compile error: can't assign func(int, int) int to func(int) int
```

函数类型的零值是nil。调用值为nil的函数值会引起panic错误：

```Go
var f func(int) int
f(3) // 此处f的值为nil, 会引起panic错误
```

函数值可以与nil比较：

```Go
var f func(int) int
if f != nil {
    f(3)
}
```

## 三、方法
从90年代早期开始，面向对象编程(OOP)就成为了称霸工程界和教育界的编程范式，所以之后几乎所有大规模被应用的语言都包含了对OOP的支持，go语言也不例外。

尽管没有被大众所接受的明确的OOP的定义，从我们的理解来讲，一个对象其实也就是一个简单的值或者一个变量，在这个对象中会包含一些方法，而一个方法则是一个一个和特殊类型关联的函数。一个面向对象的程序会用方法来表达其属性和对应的操作，这样使用这个对象的用户就不需要直接去操作对象，而是借助方法来做这些事情。

### 3.1 方法声明

在函数声明时，在其名字之前放上一个变量，即是一个方法。这个附加的参数会将该函数附加到这种类型上，即相当于为这种类型定义了一个独占的方法。 

```go
package demo

import "math"

type Point struct{ X, Y float64 }

// 函数
func Distance(p, q Point) float64 {
    return math.Hypot(q.X-p.X, q.Y-p.Y)
}

// 基于 Point 的方法
func (p Point) Distance(q Point) float64 {
    return math.Hypot(q.X-p.X, q.Y-p.Y)
}

// 基于(指针) *Point 的方法
func (p *Point) ScaleBy(factor float64) {
    p.X *= factor
    p.Y *= factor
}
```



### 3.2 嵌入式结构体(可以理解为组合或继承)

A个结构体可以嵌入到B结构体中，这样B结构体就具有A结构体的成员变量。

```go
import "image/color"

type Point struct{ X, Y float64 }

type ColoredPoint struct {
    Point
    Color color.RGBA
}
```

 我们完全可以将ColoredPoint定义为一个有三个字段的struct，但是我们却将Point这个类型嵌入到ColoredPoint来提供X和Y这两个字段。 

### 3.3 方法值与方法表达式

#### 方法值

```go
import "image/color"

type Point struct{ X, Y float64 }

func (p Point) Distance(q Point) float64 {
    return p.Distance(q)
}

func (p Point) ScaleBy(factor float64) {
    p.ScaleBy(factor)
}
```

方法调用形式为 p.Distance()， 可以将其分成两步来执行， p.Distance叫作“选择器”，选择器会返回一个方法"值"->一个将方法(Point.Distance)绑定到特定接收器变量的函数。这个函数可以不通过指定其接收器即可被调用；即调用时不需要指定接收器，因为已经在前文中指定过了，只要传入函数的参数即可 。这里与函数值使用方式类似。

```go
p := Point{1, 2}
q := Point{4, 6}

distanceFromP := p.Distance        // 方法值
fmt.Println(distanceFromP(q))      // "5"
```

#### 方法表达式

当T是一个类型时，方法表达式可能会写作`T.f`或者`(*T).f`，会返回一个函数"值"，这种函数会将其第一个参数用作接收器，所以可以用通常不写选择器的方式来对其进行调用：

```go
p := Point{1, 2}
q := Point{4, 6}

distance := Point.Distance   // 方法表达式
fmt.Println(distance(p, q))  // "5"
fmt.Printf("%T\n", distance) // "func(Point, Point) float64"

scale := (*Point).ScaleBy
scale(&p, 2)
fmt.Println(p)            // "{2 4}"
fmt.Printf("%T\n", scale) // "func(*Point, float64)"
```

## 四、封装
一个对象的变量或者方法如果对调用方是不可见的话，一般就被定义为“封装”。封装有时候也被叫做信息隐藏，同时也是面向对象编程最关键的一个方面。

Go语言只有一种控制可见性的手段：大写首字母的标识符会从定义它们的包中被导出，小写字母的则不会。

### 4.1 函数/方法封装

首字母大写的方法或函数，该方法或函数对其它包可见；反之，首字母小写的函数或方法，对其它包不可见。

### 4.2 变量封装

首字母大写的变量对其它包可见，反之相反。

## 五、接口
### 5.1 接口定义

在Go语言中接口（interface）是一种类型，一种抽象的类型， 是对其它类型行为的抽象和概括。 Go语言中接口类型的独特之处在于它是满足隐式实现的。 

Go语言提倡面向接口编程。 接口的定义形式如下：

```go
type 接口名 interface {
    方法名(参数列表) 返回值列表
    …
}
```

实现接口的条件：一个类型（type/对象），实现接口中的全部方法，那么就实现了这个接口；这是一种隐式实现。

Example：

```go
// Stringer 接口 用于将将其它类型转换成string类型
type Stringer interface {
    String() string
}
```

```go
// 结构体对象
type Point struct {
	X, Y int
}

// 实现 String() 方法，既 Point 类型隐式实现了 Stringer 接口
func (p Point) String() string {
	return fmt.Sprintf("Point->{%d, %d}", p.X, p.Y)
}
```

### 5.2 接口嵌套

 接口与接口间可以通过嵌套创造出新的接口。 新接口中包含嵌套接口的方法。

Example：

```go
// Sayer 接口
type Sayer interface {
	say()
}

// Mover 接口
type Mover interface {
	move()
}

// 接口嵌套
type animal interface {
	Sayer
	Mover
}
```

### 5.3 空接口

 空接口（interface{}）是指没有定义任何方法的接口。因此任何类型都实现了空接口。 **空接口类型的变量可以接收存储任意类型变量**。

空接口一般作为函数的参数使用，用来接收任意类型，典型的如fmt.println函数。

```go
func Println(a ...interface{}) (n int, err error) {
	return Fprintln(os.Stdout, a...)
}
```

### 5.4 类型断言

空接口可以存储任意类型的值，要想获取接口中存储的具体数据，可以通过类型断言来获取其类型和数据。

**接口值** （接口的值）是由一个 具体类型 和 具体类型的值 两部分组成。这两部分分别称为 动态类型 和 动态值。

使用类型断言判断空接口中的值，其语法格式： 

```
x.(T) // x：表示类型为interface{}的变量，T：表示断言x可能是的类型。
```

该语法返回两个值，第一个值是`x`转化为`T`类型后的变量，第二个值是一个布尔值，若为`true`则表示断言成功，为`false`则表示断言失败。 

```go
func main() {
	var x interface{}
	x = "Hello"
	v, ok := x.(string)
	if ok {
		fmt.Println(v)
	} else {
		fmt.Println("类型断言失败")
	}
}
```

## 六、反射

**reflect包** 提供了反射的相关操作。任意接口值在反射中都可以理解为由`reflect.Type`和`reflect.Value`两部分组成，并且reflect包提供了`reflect.TypeOf`和`reflect.ValueOf`两个函数来获取任意对象的Value和Type。 

### 6.1 reflect.TypeOf

使用`reflect.TypeOf()`函数可以获得任意值的类型对象（reflect.Type）。

在反射中类型还划分为两种：`类型（Type）`和`种类（Kind）`。因为在Go语言中可以使用type关键字构造很多自定义类型，而`种类（Kind）`就是指底层的类型。 

```go
var a float32 = 3.1415926
v := reflect.TypeOf(a)

fmt.Printf("type:%v kind:%v\n", t.Name(), t.Kind()) // type:float32 kind:float32
```

### 6.2 reflect.ValueOf

`reflect.ValueOf()`返回的是`reflect.Value`类型，其中包含了原始值的值信息。`reflect.Value`与原始值之间可以互相转换。 

### 6.3 反射的利弊

反射是一个强大并富有表现力的工具。但是反射不应该被滥用，原因有以下三个。

1. 基于反射的代码是极其脆弱的，反射中的类型错误会在真正运行的时候才会引发panic，那很可能是在代码写完的很长时间之后。
2. 大量使用反射的代码通常难以理解。
3. 反射的性能低下，基于反射实现的代码通常比正常代码运行速度慢一到两个数量级。



### License

MIT License GoodCoder-Notes，转载请注明出处。