# Go 基础

### 目录

- [一、程序结构](#)
  - [1.1 Hello，World](#11helloworld)
  - [1.2 包](#)
  - [1.3 声明](#)
  - [1.4 数据类型](#)
  - [1.5 关键字](#)
  - [1.6 逻辑控制](#)
- [二、函数](#)
  - [2.1 函数声明](#)
  - [2.2 可变参数](#)
  - [2.3 异常处理](#)
  - [2.4 Deferred函数](#)
  - [2.5 函数值](#)
- [三、方法](#)
  - [3.1 函数与方法区别](#)
  - [3.2 嵌入式结构体(继承)](#)
  - [3.3 方法值与方法表达式](#)
- [四、封装](#)
  - [4.1 方法封装](#)
  - [4.2 变量封装](#)
- [五、接口](#)
  - [5.1 接口定义](#)
  - [5.2 接口值](#)
  - [5.3 类型断言](#)
  - [5.4 常用接口](#)
- [六、反射](#)

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

 Go语言的代码通过**包**（package）组织，包类似于其它语言里的库（libraries）或者模块（modules）。一个包由位于单个目录下的一个或多个.go源代码文件组成, 目录定义包的作用。每个源文件都以一条`package`声明语句开始，这个例子里就是`package main`, 表示该文件属于哪个包，紧跟着一系列导入（import）的包，之后是存储在这个文件里的程序语句。 

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

`数组` 是一个由固定长度的特定类型元素组成的序列，一个数组可以由零个或多个元素组成； 因为数组的长度是固定的，因此在Go语言中很少直接使用数组。

*结构体* 是一种聚合的数据类型，是由零个或多个任意类型的值聚合成的实体。每个值称为结构体的成员。 



#### 引用类型：

**指针**  保存了所指向变量对应类型值的内存空间。

**Slice（切片）**  代表变长的序列，序列中每个元素都有相同的类型。一个slice类型一般写作[]T，其中T代表slice中元素的类型；slice的语法和数组很像，只是没有固定长度而已。

**Map（字典）**  它是一个无序的key/value对的集合，其中所有的key都是不同的，然后通过给定的key可以在常数时间复杂度内检索、更新或删除对应的value。 

**函数** 在Go语言中，函数可以当做变量一样进行传递。

**通道**  channels是它们之间的通信机制，声明方式 ch := make(chan int) // ch has type 'chan int'



#### 接口类型：

**接口类型** 具体描述了一系列方法的集合，一个实现了这些方法的具体类型是这个接口类型的实例。interface{}空接口是所有数据类型的父类型，类似于Java中的Object类。

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



### 2.2 可变参数



### 2.3 异常处理



### 2.4 Deferred函数



### 2.5 函数值



## 三、方法
### 3.1 函数与方法区别



### 3.2 嵌入式结构体(继承)



### 3.3 方法值与方法表达式



## 四、封装
### 4.1 方法封装



### 4.2 变量封装



## 五、接口
### 5.1 接口定义



### 5.2 接口值



### 5.3 类型断言



### 5.4 常用接口



## 六、反射









### License

MIT License GoodCoder-Notes，其它内容……