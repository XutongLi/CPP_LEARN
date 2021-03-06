# Lec2 - PRC and Threads

**Go** 提供了方便使用的 **RPC** ，课程中要使用 **RPC** 以使得不同机器间进行通信 。

**Go** 类型安全、内存安全，有垃圾回收机制（不必担心会对同一段内存释放两次或释放仍在使用的内存之类的问题）

 常说的 **并发 I/O** 是指，假设有一个已经启动的程序，在同一时间内，它可以通过 **RPC** 来对网络上的不同服务器进行请求 ，并且同时等待许多回复 。

可以为每一个想要启动的 **RPC** 调用都创建一条线程 ，每个线程通过 **RPC** 调用发送 **request** 消息，并且此时线程会进入等待 ，直到接收到 **response** ，继续执行 。

使用多线程的另一个原因是 **多核并行** 。如果有一些计算量很重的任务，需要大量CPU周期，使用线程可使用所在计算机上所有CPU核心上的CPU周期。若执行的是 **计算密集型任务** ，取决于机器中核心数量的限制，线程会以真正并行的方式运行 。

当一个单核计算机上运行了两个程序，操作系统会让CPU时间片在两个程序之间切换，当硬件时钟到期，操作系统会将当前运行进程的时间片交给另一个进程 。

**Go**：

声明 **map** 返回的是指针，在函数间传递也是指针

**waitGroup** 可以当做计数器来用，它不在意要计数的是什么东西

 对于 **闭包函数** ，若函数体内提到的变量在外层函数中已经声明过，但没有隐藏的话，那么内部函数会使用和外层函数 **同一个** 变量（闭包函数改变变量值，外部此变量值也改变；反之亦然） 。

闭包函数使用了外部函数的一个比那辆，编译器会分配一段 heap 内存来保存这个变量的当前值，这两个函数都会引用这个 heap 内存中所保存的这个变量（这个变量不是存在 stack 里的），当外部函数 return，这个对象仍保存在 heap 中，闭包函数仍在引用它 。GC 负责观察最后一个引用这段 heap 内存的函数什么时候 return ，最后将其释放 。

`go run -race crawler.go` 可用于检测 **race** 问题 ，它内部的工作原理是分配了某些影子内存，对于你所用到的每个内存位置，**race 检测器** 都会分配一点内存在上面，以此来跟踪线程近期所取或所写入的内存位置 ，它也会去跟踪一些线程 获取和释放锁 以及 执行其他强制线程无法进行并行操作 的同步活动 的行为 。如果race检测器检测到有块内存在进行数据的写入操作，然后其他线程也在读取时，期间并没有使用锁进行标记，检测器就会报错错误 。（不支持静态检测，只有程序运行到某段代码才可以对其检测）

如何选择使用 **mutex** 还是 **channel**：

- state -- mutex
- communication -- channels

For the 6.824 labs, I recommend sharing+locks for state, and sync.Cond or channels or time.Sleep() for waiting/notification.




