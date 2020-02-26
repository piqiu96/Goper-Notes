# Go 容器

### 目录

- [一、标准库提供的容器](#一标准库提供的容器)
  - [1.1 数组](#11-数组)
  - [1.2 切片](#12-切片)
  - [1.3 map](#13-map)
- [二、第三方库容器](#二第三方库容器)
  - [2.1 待更新](#21-)

---

## 一、标准库提供的容器

如果您在学习 Go 语言之前是从事 Java 开发，当您学完 Go 语言圣经[《The Go Programming Language》](http://gopl.io/)，在敲代码时会发现一个问题，Go 语言的标准库没有提供丰富的容器/集合库（例如：Java 中的 ArrayList，LinkedList，HashSet，TreeSet，HashMap，TreeMap）。标准库中可以用来充当容器的只有数组、切片和map，要想使用其它类型的容器可以自己去实现，但是工作量很大，而且维护成本高，也可以去使用第三方别人实现的容器框架。

希望在以后，Go 官方将容器这部分完善一下。下面看看你标准库中数组、切片和map的用法及特性。

### 1.1 数组

数组是同一种数据类型元素的集合，大小一旦初始化不可改变。**数组是值类型，赋值和传参会拷贝整个数组**。

#### 数组定义

```go
var 数组变量名 [元素个数]T	// 元素个数：必须是常量。T：数据类型
```

Example：

```go
var a [5]int
var b [6]int	// 未初始化的数组，默认值为各数据类型的默认值，int为0
```

#### 初始化

方法一：定义数组时，可以同时使用初始化列表设置元素的值。

```go
var arr = [3]int{1, 2}	// arr = [1, 2, 0]
```

方法二：不指定数组长度， 让编译器根据初始值的个数自行推断数组的长度。

```go
var arr = [...]int{1, 2}	// len(arr) = 2， "..."代表让编译器根据初始值的个数自行推断数组的长度
```

方法三：使用索引值，指定特定位置的元素来初始化数组。

```go
arr := [...]int{2:3, 4:5}	// arr = [0, 0, 3, 0, 5]
```

#### 数组遍历

```go
arr := [...]string{"北京", "上海", "深圳"}

// 方法一：for循环遍历
for i := 0; i < len(a); i++ {
	fmt.Println(a[i])
}

// 方法2：for range遍历
for index, value := range a {	// index：数组元素索引，value：元素值
	fmt.Println(index, value)
}
```

#### 多维数组

多维数组与一维数组操作类似，只是增加一步操作。

### 1.2 切片

数组的长度是固定的，这就意味着一旦数组满了，就会涉及到扩容操作，很不方便；这是数组最大的局限性。

切片（slice）是一个拥有相同类型元素的可变长度的序列。它是基于数组类型做的一层封装。它非常灵活，支持自动扩容。 

**切片是一个引用类型**，它的内部结构包含`地址`、`长度`和`容量`。切片一般用于快速地操作一块数据集合。

#### 切片定义

```go
var 切片名 []T		// T：数据类型
```

切片拥有自己的长度和容量，使用内置的len()函数获取长度，使用内置的cap()函数获取容量。 

Example:

```go
var a []string		// 声明一个字符串切片
var b = []int{}		// 声明一个整型切片并初始化为空
```

#### 基于数组定义切片

切片底层就是数组，可以使用数组定义切片。

```go
// 基于数组定义切片
a := [5]int{0, 1, 2, 3, 4}
b := a[1:4]                     // 基于数组a创建切片，包括元素a[1],a[2],a[3]
								// PS：括号两端，包含左边索引值，不包含右边索引值
fmt.Println(b)                  // [1 2 3]
fmt.Printf("type of b:%T\n", b) // type of b:[]int

// 其它方式
c := a[1:] //[1 2 3 4]
d := a[:4] //[0 1 2 3]
e := a[:]  //[0 1 2 3 4]
```

#### 基于切片再定义切片

```go
a := [...]string{"北京", "上海", "广州", "深圳", "成都", "重庆"}
// a:[北京 上海 广州 深圳 成都 重庆] type:[6]string len:6  cap:6
fmt.Printf("a:%v type:%T len:%d  cap:%d\n", a, a, len(a), cap(a))

b := a[1:3]
// b:[上海 广州] type:[]string len:2  cap:5
fmt.Printf("b:%v type:%T len:%d  cap:%d\n", b, b, len(b), cap(b))

c := b[1:5]
// c:[广州 深圳 成都 重庆] type:[]string len:4  cap:4
fmt.Printf("c:%v type:%T len:%d  cap:%d\n", c, c, len(c), cap(c))
```

#### 使用内置函数make()构造切片

上面都是基于数组来创建的切片，如果需要动态的创建一个切片，可以使用内置的`make()`函数： 

```go
// T：切片的元素类型
// size：可选值，缺省默认为0，切片中元素的数量
// cap：可选值，缺省默认为0，切片的容量
make([]T, size, cap)	
```

Example:

```go
a := make([]int, 2, 10)
```

### 切片本质

切片本质就是对底层数组的封装，它包含了三个信息：`底层数组的指针`、`切片的长度（len）`和`切片的容量（cap）`。 

一个数组`a := [8]int{0, 1, 2, 3, 4, 5, 6, 7}`，切片`s1 := a[:5]`，底层示意图如下：

<div align=center>
<img src="../../../media/pictures/go/slice_01.png" height="500"/>
</div>

#### 切片的遍历

与数组遍历方式相同，使用for或者range遍历即可。

#### appand()方法为切片添加元素

Go语言的内建函数`append()`可以为切片动态添加元素，每个切片会指向一个底层数组，这个数组的容量够用就添加新增元素。当底层数组不能容纳新增的元素时，切片就会自动按照一定的策略进行“扩容”（**扩容后都是扩容前的2倍**），此时该切片指向的底层数组就会更换。“扩容”操作往往发生在`append()`函数调用时，所以我们通常都需要用原变量接收append函数的返回值。 

```go
var numSlice []int

// 添加一个元素
numSlice = append(numSlice, 0)
fmt.Printf("%v  len:%d  cap:%d\n", numSlice, len(numSlice), cap(numSlice))	// [0]  len:1  cap:1

// 添加多个元素
numSlice = append(numSlice, 1, 2, 3)
fmt.Printf("%v  len:%d  cap:%d\n", numSlice, len(numSlice), cap(numSlice))	// [0 1 2 3]  len:4  cap:4

// 添加一个数组
arr := [...]int{4, 5}
numSlice = append(numSlice, arr...)
fmt.Printf("%v  len:%d  cap:%d\n", numSlice, len(numSlice), cap(numSlice))	// [0 1 2 3 4 5]  len:6  cap:8
```

#### 从切片中删除元素

Go语言中并没有为删除切片元素提供方法，appand()来实现删除元素的功能。

```go
// 从切片中删除元素
a := []int{30, 31, 32, 33, 34, 35, 36, 37}
// 要删除索引为2的元素
a = append(a[:2], a[3:]...)
fmt.Println(a) //[30 31 33 34 35 36 37]
```

### 1.3 map

`map` 为键值对容器，内部使用散列表实现；

它是一种无序的基于`key-value`的数据结构，是**引用类型**，必须初始化才能使用。

#### 声明及初始化

```go
// 声明
map[KeyType]ValueType	// KeyType:表示键的类型。ValueType:表示值的类型。

// 初始化
make(map[KeyType]ValueType, [cap])
```

Example:

```go
scoreMap := make(map[string]int, 8)
scoreMap["张三"] = 90
scoreMap["小明"] = 100
fmt.Println(scoreMap)			// map[小明:100 张三:90]
fmt.Println(scoreMap["小明"])	   // 100
```

#### 使用delete()函数删除键值对

```go
delete(map, key)
```

#### 判断某个键是否存在

```go
value, ok := map[key]	// 如果key存在ok为true，v为对应的值；不存在ok为false,v为值类型的零值
```

## 二、第三方库容器
### 2.1 待更新

因为还没有确定那个第三方容器包社区活跃且性能优秀，无Bug。所以确定下来后再更新次部分。

给出一个参考的第三方容器包地址：

https://goframe.org/container/gmap/index 



### License

MIT License GoodCoder-Notes，转载请注明出处。