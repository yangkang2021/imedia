# go语言四剑客：goroutine-channel-timer-select
>1. 全部围绕使用channel进行协程通讯展开
>2. 这四个组件很特别，且使用非常的广泛

### (一)协程goroutine
1. 主程序退出，协程也会马上退出。 不管协程执行到哪儿，所以可能需要等待。等待方式：
    - sync.WaitGroup
    - context
2. 协程配置调度
    - runtime.NumCPU()
    - runtime.GOMAXPROCS(n) //之前运行协程的最大线程数数量。 可以控制不占太多cpu
    - runtime.Gosched()//让出当前cpu
    - runtime.Goexit()//协程退出
### (二)管道channel
1. 作用：
    - 协程间的数据同步和共享
    - 阻塞等待：等到有写入。
2. 操作：
    - 创建：make(chan int, n),n指定缓冲区大小,0表示无缓冲区管道要求读与写在不同协程已经都准备好了。
    - 写入：可一直写到缓冲区满，然后阻塞，被close后写报pannic。nil管道读写都会阻塞。
    - 读取/for-range：可一直读到缓冲区空，然后阻塞，被close后可继续读到空，之后也不会阻塞。nil管道读写都会阻塞。
    - 关闭：退出或者完成写入，必须close，不然读取会阻塞。

### (三)定时器timer
> 都是基于channel，时间到了,内部的channel对象C有数据可读。
1. 单次的定时器--实现延迟的三种方式
    - time.NetTimer(时长); <-t.C
    - <-time.After(时长)
    - time.Sleep(时长)
2. 重复的定时器--实现周期性工作
    - time.NetTicker(时长); 循环读<-t.C
> 不用了一定要stop

### (四)select
> 用来等待channel可读，可以同时等待多个。
1. 类似多路复用：同时读取多个channle，有一个可读就可以
2. 实现超时机制：同时select等待：工作协程的channel写入和timer的channel写入。
3. select{}:永久阻塞。
    
    
    

