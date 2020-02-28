# Go 文件管理

### 目录

- [一、文件打开方式](#一文件打开方式)
  - [1.1 os.open()函数](#11-osopen函数)
  - [1.2 os.openFile()函数](#12-osopenFile函数)
- [二、文件读写](#二文件读写)
  - [2.1 文件读操作](#21-文件读操作)
  - [2.2 文件写操作](#22-文件写操作)
- [三、带缓冲区的文件读写](#三带缓冲区的文件读写)
- [四、ioutil工具包使用](#四ioutil工具包使用)

---

## 一、文件打开方式

计算机中的文件是存储在外部介质（通常是磁盘）上的数据，文件分为文本文件和二进制文件。Go 语言中提供两个函数用于打开本地文件：`os.Open(name string)`和`os.OpenFile(name string, flag int, perm FileMode)`，都会返回`*File`和`error`，不同点在与Open打开的文件是只读的，OpenFile打开的文件是可读可写的；在源码中Open函数是通过调用OpenFile函数实现的。

进行文件操作，对文件进行一系列操作完毕后，不再使用这个文件时，文件必须关闭，调用文件实例的`close()`方法能够关闭文件。

### 1.1 os.open()函数

`os.Open(name string)`通过一个文件路径名打开一个文件，返回`*File`和`error`。这个`*File`实例是只读的，不能进行写操作。源码如下：

```go
func Open(name string) (*File, error) {
	return OpenFile(name, O_RDONLY, 0)
}
```

示例：

```go
package main

import (
	"log"
	"os"
)

func main() {
	// 只读方式打开当前目录下的main.go文件
	file, err := os.Open("./main.go")
    // 错误处理
	if err != nil {
		log.Fatalln("open file failed, err:", err)
	}
	// 关闭文件
	defer file.Close()
}
```

### 1.2 os.OpenFile()函数

`os.OpenFile()`函数能够以指定模式打开文件，从而实现文件的读写操作。

```go
func OpenFile(name string, flag int, perm FileMode) (*File, error) {
	...
}
```

函数的参数：

`name`：要打开的文件名；

`flag`：打开文件的模式，具体模式如下：（源码）

```go
const (
    // 必须指定O_RDONLY、O_WRONLY或O_RDWR中的一个。
	O_RDONLY int = syscall.O_RDONLY // 只读
	O_WRONLY int = syscall.O_WRONLY // 只写
	O_RDWR   int = syscall.O_RDWR   // 读写
	// 剩余的值可以是或“嵌入”以控制行为。
	O_APPEND int = syscall.O_APPEND // 追加数据
	O_CREATE int = syscall.O_CREAT  // 创建文件，如果文件不存在
	O_EXCL   int = syscall.O_EXCL   // 与O_CREATE一起使用, 文件必须不存在
	O_SYNC   int = syscall.O_SYNC   // 同步I/O
	O_TRUNC  int = syscall.O_TRUNC  // 清空
)
```

`perm`： 文件权限，一个八进制数。r（读）04，w（写）02，x（执行）01。 与Linux文件权限很相似。

示例：

```go
package main

import (
	"log"
	"os"
)

func main() {
	// 打开当前目录下的main.go文件
	file, err := os.OpenFile("./main.go", os.O_RDONLY, 0666)
    // 错误处理
	if err != nil {
		log.Fatalln("open file failed, err:", err)
	}
	// 关闭文件
	defer file.Close()
}
```

## 二、文件读写

### 2.1 文件读操作

```go
package main

import (
	"fmt"
	"io"
	"log"
	"os"
)

func main() {
	// 只读方式打开当前目录下的main.go文件
	file, err := os.Open("main.go")
	if err != nil {
		log.Fatalln("open file failed, err:", err)
	}
	// 关闭文件
	defer file.Close()

	var content []byte
	var tmp = make([]byte, 1024)
	for {
		n, err := file.Read(tmp)
		if err == io.EOF {
			fmt.Println("文件读取完毕...")
			break
		}
		if err != nil {
			log.Fatalln("read file failed, err:", err)
		}

		content = append(content, tmp[:n]...)
	}

	fmt.Printf("读取到如下内容：\n %s", content)
}
```

### 2.2 文件写操作

```go
package main

import (
	"log"
	"os"
)

func main() {
	// 只读方式打开当前目录下的main.go文件
	file, err := os.OpenFile("copyformain.txt", os.O_CREATE | os.O_WRONLY | os.O_TRUNC, 0666)
	if err != nil {
		log.Fatalln("open file failed, err:", err)
	}
	// 关闭文件
	defer file.Close()
	str := "北京上海广州\n"
	file.Write([]byte(str))
	file.WriteString("长江黄河珠三角")
}
```

## 三、带缓冲区的文件读写

bufio是在file的基础上封装了一层API，支持更多的功能。使用缓冲区从而提高对文件的读写速度。
```go
package main

import (
	"bufio"
	"io"
	"log"
	"os"
)

func main() {
	// 创建只读文件
	srcFile, err := os.Open("main.go")
	if err != nil {
		log.Fatalln("open file failed, err:", err)
	}
	// 关闭文件
	defer srcFile.Close()

	// 创建待写入文件
	destFile, err := os.OpenFile("copyformain.txt", os.O_CREATE | os.O_WRONLY | os.O_TRUNC, 0666)
	if err != nil {
		log.Fatalln("open file failed, err:", err)
	}
	// 关闭文件
	defer destFile.Close()

	// 创建缓冲区
	srcReader := bufio.NewReader(srcFile)
	destWriter := bufio.NewWriter(destFile)

	for {
		line, err := srcReader.ReadSlice('\n')
		if err == io.EOF {
			break
		}
		destWriter.Write(line)
		destWriter.Flush()
	}
}
```

## 四、ioutil工具包使用

```go
package main

import (
	"io/ioutil"
	"log"
)

func main() {
	// 使用 ioutil 读取整个文件
	srcFile, err := ioutil.ReadFile("main.go")
	// 错误处理
	if err != nil {
		log.Fatalln("read file failed, err:", err)
	}

	// 将读取到字节数组写入到文件中
	err = ioutil.WriteFile("copyformain.txt", srcFile, 0666)
	// 错误处理
	if err != nil {
		log.Fatalln("write file failed, err:", err)
	}
}
```



### License

MIT License GoodCoder-Notes，转载请注明出处。