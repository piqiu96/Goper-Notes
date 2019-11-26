## 原理

- select是随机的还是顺序的？
- Go语言局部变量分配在栈还是堆？
- 介绍下你平时都是怎么调试 golang 的 bug 以及性能问题的?
- 简单介绍下 golang 中 make 和 new 的区别？
- 无缓冲 Chan 的发送和接收是否同步?
- JSON 标准库对 nil slice 和 空 slice 的处理是一致的吗？
- 协程，线程，进程的区别？ 
- Epoll原理？
- Go语言的栈空间管理是怎么样的?
- 滑动窗口的概念以及应用?
- Go的调度原理？
- go struct能不能比较？
- select可以用于什么?
- context包的用途是什么?
- client如何实现长连接?
- slice，len，cap，共享，扩容？
- map如何顺序读取?
- 实现set？
- go defer（for defer）？
- time.Now()返回的是什么时间？这样做的决定因素是什么?
- golang 的 sync.atomic 和 C++11 的 atomic 最显著的在 golang doc 里提到的差别在哪里？



## 内存 & 垃圾回收

- Golang的内存模型，为什么小对象多了会造成gc压力？
- 简述一下你对Go垃圾回收机制的理解？
- Golang GC 时会发生什么?
- Golang 里的逃逸分析是什么？怎么避免内存逃逸？
- Golang 的GC触发时机是什么?



## 多线程 & 并发
- 并发编程概念是什么？
- 简述一下golang的协程调度原理?
- 介绍下 golang 的 runtime 机制?
- 如何获取 go 程序运行时的协程数量, gc 时间, 对象数, 堆栈信息?
- Golang中除了加Mutex锁以外还有哪些方式安全读写共享变量？
- go语言的并发机制以及它所使用的CSP并发模型？
- Golang 中常用的并发模型？
- 互斥锁，读写锁，死锁问题是怎么解决？
- Data Race问题怎么解决？能不能不加锁解决这个问题？
- 什么是channel，为什么它可以做到线程安全？
- Golang 中 Goroutine 如何调度?
- 分布式锁实现原理，用过吗？
- Goroutine和Channel的作用分别是什么?
- 怎么查看Goroutine的数量?
- 说下Go中的锁有哪些?三种锁，读写锁，互斥锁，还有map的安全的锁?
- 读写锁或者互斥锁读的时候能写吗?
- 怎么限制Goroutine的数量？
- Channel是同步的还是异步的？
- 说一下异步和非阻塞的区别?
- Log包线程安全吗？
- Goroutine和线程的区别?



## 其他 

高级：
让你设计一个web框架，你要怎么设计，说一下步骤？  
说一下中间件原理？    



负载均衡：
负载均衡原理是什么?  
Etcd怎么实现分布式锁?    

