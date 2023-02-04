netty特性

1. 异步事件驱动
2. 可维护性
3. 高性能协议 服务器和客户端构建

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050133878.png)

在此引入前置知识:NIO 三大剑客

**channle,buffer,selector**



buffer读写 单字节读写,性能差

![buffer读写](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050133238.png)

由上述问题引入缓冲区(JVM底层支持传入数组,性能有一定提升)  

![file字符流](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050133823.png)

提炼成模型则是NIO模型

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050133686.png)





1. directByteBuffer自动回收的条件：当堆内存中的对象被GC掉之后，会进行回收





## 看源码心得

1. 看核心接口
2. 根据接口看实现了什么功能<font color=red>注意看抽象类的实现</font>



## netty

1. netty的线程池:io.netty.buffer.PooledByteBufAllocator

2. io.netty.channel.EventLoopGroup 事件循环组原理

3. io.netty.util.concurrent.EventExecutorGroup，有执行调度的基本功能，的next方法获取其中一个组中EventExecutor，EventExecutor是EventExecutorGroup中专门执行事件的执行器，是一个特殊的ExecutorGroup，是组里套组，组中只有一个线程，作用是将相关的线程

4. EventLoopGroup 为了注册通道，将相关的线程连接起来，通过next循环线程数组

5. io.netty.util.concurrent.AbstractEventExecutorGroup  重点方法（看其中的方法实现）

   ![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050133289.png)

   

6. 在uninx和类uninx中线程和进程是一个东西，线程只是共享了一部分东西（真实的东西）  理论是线程由进程组成

7. 泛型的原理：泛型檫除？？（==百度一下==）

   - 怎么确定泛型：

8. 看源码观察者模式，责任链模式（Netty用的很多）

   

Excutor 接口提供基本功能（submit）

ExcutorService 提供一堆服务

ScheduleService 对excutuorService进行组合，提供周期性执行的功能

基于此三个接口，netty实现自己的ExcutorGroup顶层netty接口，但是ExcutorGroup只是定义，并没有实现线程有什么方式组成（数组，链表等？） 此时就引入了eventLoopGroup

**惊群**：重新创建selector





Selector是线程安全的，但是selectedKeys不是线程安全的 

netty伙伴算法



​    
