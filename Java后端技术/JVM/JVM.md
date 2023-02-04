

## JVM基础知识

### java从编码到执行

java可以混合执行

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050109054.png)

### 什么是JVM

**jvm是跨语言的平台**（是一种规范，不止java，其他满足可以变成class文件，就可以在jvm执行）

### 常见的JVM

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050109585.png)

## ClassFileFormat(Class文件)

## Class文件加载-初始化

1. loading ：将class文件加载到内存中

2. linking: 

	1.  Verification ：对文件校验，校验是不是class文件，class版本

	2. Preparation：class文件中将静态成员变量赋默认值

	3. Resolution: 将类、方法、属性等符号引用解析成地址引用，

		​					常量池中的而各种符号引用解析为指针，偏移量等地址的直接引用

3. initializing: 调用静态代码块，同时将静态成员变量赋初始值

### 类加载机制

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050109481.png)

**使用双亲委派（会指定上级parent加载器）**

- 安全问题（避免自定义的类随意加载内存）

  如果自定义类如java.lang.String等基础数据类型，自定义的类可能存在安全问题（发送string类型密码等）

- 提升效率（父加载相关类后就不需要在加载）

  > 重写classLoader方法会破坏双亲委派机制
  >
  > - 如何打破：重写loadClass（），指定自己的parentclassLoader
  > - 何时打破过：
  >   - JDK1.2之前，自定义ClassLoader都必须重写loadClass()（因为无findClass方法）
  >   - ThreadContextClassLoader可以实现基础类调用实际类代码，通过thread.setContextClassLoader指定
  > - 热部署热启动
  >   - orsi Tomcat都有自己自定义的ClassLoader（可以在机载同一类库的不同版本）
  >
  > 自定义classLoader可以实现jar包加密

### 什么时候需要自己使用classload的loadclass方法加载类

spring中存在动态代理的时候，spring会调用loadclass方法将动态代理生成的类load到内存中

###  自定义类加载器

原理:findInCash->parent.loadClass ->findClass

只需要实现loadclass的findClass方法（模版函数/钩子函数）,在findClass中defineClass(class文件的byte[]->Class对象)

### 设置编译模式

- 默认为混合模式（解释和编译混合）

	 检测热点代码: -XX:CompileThreshold=1000(可进行相关调整)

- -Xint 解释执行（需要的时候执行编译，启动快，运行慢）

- -Xcomp 编译执行（编译慢）

#### hashcode

####  锁的信息（2位 四种组合）

**GC信息（年龄）**
如果是数组，数组的长度

## JMM

### 硬件的缓存一致性

协议很多

intel 用MESI

https://www.cnblogs.com/z00377750/p/9180644.html

现代CPU的数据一致性实现 = 缓存锁(MESI ...) + 总线锁

读取缓存以cache line为基本单位，目前64bytes

位于同一缓存行的两个不同数据，被两个不同CPU锁定，产生互相影响的伪共享问题

伪共享问题：JUC/c_028_FalseSharing

**使用缓存行的对齐能够提高效率（会存在一定的空间浪费）**

### 乱序问题

CPU为了提高指令执行效率，会在一条指令执行过程中（比如去内存读数据（慢100倍）），去同时执行另一条指令，前提是，两条指令没有依赖关系

https://www.cnblogs.com/liushaodong/p/4777308.html

写操作也可以进行合并

https://www.cnblogs.com/liushaodong/p/4777308.html

JUC/029_WriteCombining

乱序执行的证明：JVM/jmm/Disorder.java

原始参考：https://preshing.com/20120515/memory-reordering-caught-in-the-act/

### 如何保证特定情况下不乱序

#### 硬件(CPU)内存屏障 X86

>  sfence:  store| 在sfence指令前的写操作当必须在sfence指令后的写操作前完成。
>  lfence：load | 在lfence指令前的读操作当必须在lfence指令后的读操作前完成。
>  mfence：modify/mix | 在mfence指令前的读写操作当必须在mfence指令后的读写操作前完成。

> 原子指令，如x86上的”lock …” 指令是一个Full Barrier，执行时会锁住内存子系统来确保执行顺序，甚至跨多个CPU。Software Locks通常使用了内存屏障或原子指令来实现变量可见性和保持程序顺序

#### JVM级别如何规范（JSR133）

> LoadLoad屏障：
> 	对于这样的语句Load1; LoadLoad; Load2， 
>
> 	在Load2及后续读取操作要读取的数据被访问前，保证Load1要读取的数据被读取完毕。
>
> StoreStore屏障：
>
> 	对于这样的语句Store1; StoreStore; Store2，
> 																				
> 	在Store2及后续写入操作执行前，保证Store1的写入操作对其它处理器可见。
>
> LoadStore屏障：
>
> 	对于这样的语句Load1; LoadStore; Store2，
> 																				
> 	在Store2及后续写入操作被刷出前，保证Load1要读取的数据被读取完毕。
>
> StoreLoad屏障：
> 	对于这样的语句Store1; StoreLoad; Load2，
>
> ​	 在Load2及后续所有读取操作执行前，保证Store1的写入对所有处理器可见。

#### volatile的实现细节

1. 字节码层面
	ACC_VOLATILE

2. JVM层面
	volatile内存区的读写 都加屏障

	> StoreStoreBarrier
	>
	> volatile 写操作
	>
	> StoreLoadBarrier

	> LoadLoadBarrier
	>
	> volatile 读操作
	>
	> LoadStoreBarrier

3. OS和硬件层面
	https://blog.csdn.net/qq_26222859/article/details/52235930
	hsdis - HotSpot Dis Assembler
	windows lock 指令实现 | MESI实现

#### synchronized实现细节

1. 字节码层面
	ACC_SYNCHRONIZED
	monitorenter monitorexit
2. JVM层面
	C C++ 调用了操作系统提供的同步机制
3. OS和硬件层面
	X86 : lock cmpxchg / xxx
	[https](https://blog.csdn.net/21aspnet/article/details/88571740)[://blog.csdn.net/21aspnet/article/details/](https://blog.csdn.net/21aspnet/article/details/88571740)[88571740](https://blog.csdn.net/21aspnet/article/details/88571740)

## 对象

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050109697.png)

### 对象的创建过程

1. class loading

2. class linking（verification，preparation，resolution）

3. class initializing

	**前三步是将class文件load到内存中**

4. 申请对象内存

5. 成员变量赋默认值

6. 调用构造方法

	1. 成员变量顺序赋初始值
	2. 执行构造方法语句

### 对象在内存中的存储布局（64为操作系统）

```
#观察虚拟机配置
java  -XX:+PrintCommandLineFlags -version
```

#### 普通对象

1. 对象头： markword 8字节
2. ClassPointer指针：指向class对象   -XX:+UseCompressedClassPointers 为4字节 不开启为8字节
3. 实例数据
	1. 引用类型：-XX:+UseCompressedOops 为4字节 不开启为8字节 
4. Padding对齐:8的倍数（保证效率，是8的倍数）

#### 数组对象

1. 对象头:markword 8字节
2. ClassPointer: 指向class对象   -XX:+UseCompressedClassPointers 为4字节 不开启为8字节
3. 数组长度:4字节
4. 数据数据：
5. Padding对齐:8的倍数（保证效率，是8的倍数）

### 对象头具体包括什么

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050109114.jpg)

hashCode部分：
		31位hashcode -> System.identityHashCode(…)

按原始内容计算的hashcode，重写过的hashcode方法计算的结果不会存在这里。

当一个对象计算过identityHashCode之后，不能进入偏向锁状态

什么时候会产生hashcode?当然是调用未重写的hashcode()方法以及System.identityHashCode的时候

> GC年龄默认是15是因为分带年龄只有4位，只能表示15位

参考资料

https://cloud.tencent.com/developer/article/1480590

https://cloud.tencent.com/developer/article/1484167

https://cloud.tencent.com/developer/article/1485795

https://cloud.tencent.com/developer/article/1482500



### 对象怎么定位（对象怎么分配）

两种是实现方式（看JVM的实现方式）

1. 句柄池：指针池  间接指针，节省内存，内存回收的时候快

	![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050110863.png)

2. 直接指针:访问速度快

	![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050110938.png)

参考资料

https://blog.csdn.net/clover_lily/article/details/80095580

### Object o=new Object（）在内存中占用多少个字节

在64为虚拟机中，Hotspot默认配置下占用16个字节

那么首先new Object（）占用8(markword)+8(class pointer)+0(instance data)+0(补齐为8的倍数)16个字节，然后加引用（因为jvm默认开启UseCompressedClassPointers类型指针压缩，所以默认引用是占4字节，但这里没启用压缩，所以为8字节）占的8个字节=24个字节

## JAVA runtime Data Area

分析 JVM 内存结构，主要就是分析 JVM 运行时数据存储区域。JVM 的运行时数据区主要包括：**堆、栈、方法区、程序计数器**等。而 JVM 的优化问题主要在**线程共享的数据区**中：**堆、方法区**。

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050110611.png)

### 栈帧

**每一个方法都有一个栈帧**

1. Local variable table（局部变量表）

	**方法局部变量存放在局部变量表中**

2. Operand Stack（操作数栈）

	对于long的处理（store and load），多数虚拟机的实现都是原子的
	jls 17.7，没必要加volatile

3. Dynamic Linking

	https://blog.csdn.net/qq_41813060/article/details/88379473 

4. return address

	a() -> b()，方法a调用了方法b, b方法的返回值放在什么地方

	

### Heap

1. Method Area

	字符串常量位于Perm Space (JDK<1.8)FGC不会清理大小启动的时候指定，不能变

	字符串常量位于堆 Meta Space (>=1.8),会触发FGC清理。不设定的话，最大就是物理内存

2.  Runtime Constant Pool

3. Native Method Stack

4. Direct Memory

## JVM

### JVM如何找到垃圾

**原文地址**:https://blog.csdn.net/weixin_39928844/article/details/110158674

1. 引用计数（ReferenceCount）
2. 根可达算法(RootSearching)

#### 引用计数

引用计数法设定给对象中添加一个**引用计数器**，每当有一个地方引用它时，计数器值加１；当引用失效时，计数器值减1，引用数量为0的时候，则说明对象没有被任何引用指向,，可以认定是“垃圾”对象。**引用计数法不能解决循环引用的问题**。例如O1->O2->O3->O1

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050110410.png)

#### 根可达算法

其算法思路就是通过一系列名为 **GC Roots** 的对象作为根，从根上开始向下搜索，搜索所走过的路径称为**引用链**(Reference Chain)，当一个对象到 **GC Roots** 没有任何引用链相连时，则证明此对象是不可用的。

##### 常见的GC roots

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050111651.png)

- **线程栈变量**

Java程序从main方法开始执行，main方法会开启一个线程，这个线程里有线程栈，里面有栈帧。

从main开始这个线程栈帧里面的这些个叫做根对象。

- **静态变量**

一个class被load到内存之后，马上就对静态变量进行初始化，所以静态变量访问到的对象也是根对象。

- **常量池**

如果一个class能够用到其他的class的对象，那么他就是根对象。

- **JNI指针**

本地方法用到本地的对象也是根对象。

### 常见的垃圾回收算法

- 标记清除(mark sweep) - 位置不连续 产生碎片 效率偏低（两遍扫描）
- 拷贝算法 (copying) - 没有碎片，浪费空间
- 标记压缩(mark compact) - 没有碎片，效率偏低（两遍扫描，指针需要调整）

#### 标记清除

先标记垃圾，然后清除垃圾。通过该算法，先找到那些有用的对象，没有用的并出来然后把它们清除掉。

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050111999.png)

从图中可以看出，标记清除算法将垃圾标记并清除后，内存中原先不可用的内存变成了空闲的可用的，但是这些内存有些有些不连续了，也就是说产生了**碎片**。

再一个，如果存活的对象比较多，这种情况标记清除算法的执行效率比较高。相反执行效率就稍微低一点，因为需要两遍扫描，第一次扫描找到那些有用的，第二次扫描把那些没用的找出来清理掉。

#### 拷贝算法

拷贝算法，就是**把内存一分为二**，比如A、B两个区域，分开之后，把A区域中有用的拷贝到B区域，拷贝完成后，把A区域全部清除，下次再分配内存的时候先往B区域分配，如此往复。

1) **把内存一分为二**，分为A、B两个区域，分配内存先往A区域分配

	![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050111839.png)

2) **拷贝A区域中存活的有用的对象到B区域**

	![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050111406.png)

拷贝完成的状态：

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050112084.png)

3. **将A区域全部清除**(内存全部释放)

	![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050112422.png)

4. 之后**再分配内存的时候往B区域分配**

	清除B之后再继续往A区域分配，如此往复，拷贝来拷贝去


该算法**适用于存活对象较少的情况**，只扫描一次，**效率有所提高并且没有产生内存碎片**。需要注意的是，移动复制对象时须调整对象引用。
缺点也显而易见，得准备两份内存，**浪费空间**。

#### 标记压缩

对象分配到内存之后，需要回收的时候，先把没有引用指向的对象**标记为垃圾**，然后**把后面存活的对象拷贝到标记的那个地方**，不仅如此，最后**凡是有用的对象全部移到前面，无论这个内存是没有使用，都压缩整理到前面**，最后剩下的大块空间就全部清理出来了。

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050112360.png)

可以看到，标记压缩进行回收垃圾之后，空间连续，没有碎片。

我们分析一下该算法的实现思路，

1)通过GC Roots找到那些有用的不可回收的

2)把不可回收的有用对象往前移动

所以肯定需要扫描两次内存，而且还要移动对象，第一次扫描先找游泳对象，第二次扫描移动对象。

移动的过程中，如果是多线程还要考虑线程同步，所以标记压缩算法效率上要低一些。

**该算法的优点在于对象分配不会产生内存减半，而且不会产生内存碎片**

### 堆内存逻辑分区（不使用部分带的垃圾回收器）

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050112164.png)

1. 部分垃圾回收器使用的模型


> 除Epsilon ZGC Shenandoah之外的GC都是使用逻辑分代模型
>
> **G1是逻辑分代，物理不分代**
>
> 除此之外不仅逻辑分代，而且物理分代

2. 新生代 + 老年代 + 永久代（1.7）Perm Generation/ 元数据区(1.8) Metaspace
	1. 永久代 元数据 - Class
	2. 永久代必须指定大小限制 ，元数据可以设置，也可以不设置，无上限（受限于物理内存）
	3. 字符串常量 1.7 - 永久代，1.8 - 堆
	4. MethodArea逻辑概念 - 永久代、元数据

3. 新生代 = Eden + 2个suvivor区 
	
	```
	#新生代和老年代比例为1：2（JDK1.8）
	java -XX:+PrintFlagsFinal -version |grep NewRatio
	```

	1. YGC回收之后，大多数的对象会被回收，活着的进入s0
	2. 再次YGC，活着的对象eden + s0 -> s1
	3. 再次YGC，eden + s1 -> s0
	4. 年龄足够 -> 老年代 （15 CMS 6）
	5. s区装不下 -> 老年代
	
4. 老年代

	1. 顽固分子
	2. 老年代满了FGC Full GC

5. GC Tuning (Generation)

	1. 尽量减少FGC
	2. MinorGC = YGC
	3. MajorGC = FGC

6. 对象分配过程图

	![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050112083.png)

7. 动态年龄：（不重要）
	https://www.jianshu.com/p/989d3b06a49d

8. 分配担保：（不重要）
	YGC期间 survivor区空间不够了 空间担保直接进入老年代
	参考：https://cloud.tencent.com/developer/article/1082730

### 常见的垃圾回收器

![](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050112963.png)

1. JDK诞生 Serial追随 提高效率，诞生了PS，为了配合CMS，诞生了PN，CMS是1.4版本后期引入，CMS是里程碑式的GC，它开启了并发回收的过程，但是CMS毛病较多，因此目前任何一个JDK版本默认是CMS
	并发垃圾回收是因为无法忍受STW
2. Serial 年轻代 串行回收
3. PS 年轻代 并行回收
4. ParNew 年轻代 配合CMS的并行回收
5. SerialOld 
6. ParallelOld
7. ConcurrentMarkSweep 老年代 并发的， 垃圾回收和应用程序同时运行，降低STW的时间(200ms)
	CMS问题比较多，所以现在没有一个版本默认是CMS，只能手工指定
	CMS既然是MarkSweep，就一定会有碎片化的问题，碎片到达一定程度，CMS的老年代分配对象分配不下的时候，使用SerialOld 进行老年代回收
	想象一下：
	PS + PO -> 加内存 换垃圾回收器 -> PN + CMS + SerialOld（几个小时 - 几天的STW）
	几十个G的内存，单线程回收 -> G1 + FGC 几十个G -> 上T内存的服务器 ZGC
	算法：三色标记 + Incremental Update
8. G1(10ms)
	算法：三色标记 + SATB
9. ZGC (1ms) PK C++
	算法：ColoredPointers + LoadBarrier（读屏障）
10. Shenandoah
	算法：ColoredPointers + WriteBarrier（写屏障）
11. Eplison
12. PS 和 PN区别的延伸阅读：
	[https://docs.oracle.com/en/java/javase/13/gctuning/ergonomics.html#GUID-3D0BB91E-9BFF-4EBB-B523-14493A860E73](https://docs.oracle.com/en/java/javase/13/gctuning/ergonomics.html)
13. 垃圾收集器跟内存大小的关系
	1. Serial 几十兆
	2. PS 上百兆 - 几个G
	3. CMS - 20G
	4. G1 - 上百G
	5. ZGC - 4T - 16T（JDK13）

1.8默认的垃圾回收：**PS + ParallelOld**

## JVM调优

### 常见的垃圾回收器组合参数设定（1.8）

* -XX:+UseSerialGC = Serial New (DefNew) + Serial Old
	* 小型程序。默认情况下不会是这种选项，HotSpot会根据计算及配置和JDK版本自动选择收集器
* -XX:+UseParNewGC = ParNew + SerialOld
	* 这个组合已经很少用（在某些版本中已经废弃）
	* https://stackoverflow.com/questions/34962257/why-remove-support-for-parnewserialold-anddefnewcms-in-the-future
* -XX:+UseConc<font color=red>(urrent)</font>MarkSweepGC = ParNew + CMS + Serial Old
* -XX:+UseParallelGC = Parallel Scavenge + Parallel Old (1.8默认) 【PS + SerialOld】
* -XX:+UseParallelOldGC = Parallel Scavenge + Parallel Old
* -XX:+UseG1GC = G1
* Linux中没找到默认GC的查看方法，而windows中会打印UseParallelGC 
	* java +XX:+PrintCommandLineFlags -version
	* 通过GC的日志来分辨
* Linux下1.8版本默认的垃圾回收器到底是什么？

	* 1.8.0_181 默认（看不出来）Copy MarkCompact
	* 1.8.0_222 默认 PS + PO

### 常用的JVM命令行参数

* JVM的命令行参数参考：https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html

* HotSpot参数分类

	> 标准： - 开头，所有的HotSpot都支持
	>
	> 非标准：-X 开头，特定版本HotSpot支持特定命令
	>
	> 不稳定：-XX 开头，下个版本可能取消

	java -version

	java -X

	#### 重点参数

	1. 区分概念：内存泄漏memory leak，内存溢出out of memory（内存不够用），内存泄漏不一定会产生溢出
	2. java -XX:+PrintCommandLineFlags HelloGC
	3. java -Xmn10M -Xms40M -Xmx60M -XX:+PrintCommandLineFlags -XX:+PrintGC  HelloGC
		PrintGCDetails （详细信息）PrintGCTimeStamps （GC时间）PrintGCCauses（产生GC原因）
	4. java -XX:+UseConcMarkSweepGC -XX:+PrintCommandLineFlags HelloGC
	5. java -XX:+PrintFlagsInitial 默认参数值
	6. java -XX:+PrintFlagsFinal 最终参数值
	7. java -XX:+PrintFlagsFinal | grep xxx 找到对应的参数
	8. java -XX:+PrintFlagsFinal -version |grep GC

### GC日志详情

![GC日志详解](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050113515.png)

heap dump部分：

```
eden space 5632K, 94% used [0x00000000ff980000,0x00000000ffeb3e28,0x00000000fff00000)
                            后面的内存地址指的是，起始地址，使用空间结束地址，整体空间结束地址
```

![GCHeapDump](https://raw.githubusercontent.com/CNRF/noteImage/main/image/202302050113491.png)

### 调优的步骤

* 调优，从业务场景开始，没有业务场景的调优都是耍流氓

* 无监控（压力测试，能看到结果），不调优

* 步骤：

	1. 熟悉业务场景（没有最好的垃圾回收器，只有最合适的垃圾回收器）
		1. 响应时间、停顿时间 [CMS G1 ZGC] （需要给用户作响应）
		2. 吞吐量 = 用户时间 /( 用户时间 + GC时间) [PS]
	2. 选择回收器组合
	3. 计算内存需求（经验值 1.5G 16G）
	4. 选定CPU（越高越好）
	5. 设定年代大小、升级年龄
	6. 设定日志参数
		1. -Xloggc:/opt/xxx/logs/xxx-xxx-gc-%t.log -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=20M -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCCause
		2. 或者每天产生一个日志文件
	7. 观察日志情况

### 优化环境

1. 有一个50万PV的资料类网站（从磁盘提取文档到内存）原服务器32位，1.5G
		的堆，用户反馈网站比较缓慢，因此公司决定升级，新的服务器为64位，16G
		的堆内存，结果用户反馈卡顿十分严重，反而比以前效率更低了
		1. 为什么原网站慢?
			很多用户浏览数据，很多数据load到内存，内存不足，频繁GC，STW长，响应时间变慢
		1. 为什么会更卡顿？
		内存越大，FGC时间越长
		1. 咋办？（切换JVM垃圾回收器）
		PS -> PN + CMS 或者 G1
	
2. **系统CPU经常100%，如何调优？(面试高频)**

   CPU100%那么一定有线程在占用系统资源，

   1. 找出哪个进程cpu高（top）
   2. 该进程中的哪个线程cpu高（top -Hp）
   3. 导出该线程的堆栈 (jstack)
   4. 查找哪个方法（栈帧）消耗时间 (jstack)
   5. 工作线程占比高 | 垃圾回收线程占比高

3. **系统内存飙高，如何查找问题？（面试高频）**
	
	1. 导出堆内存 (jmap命令导出)
	2. 通过工具分析(jhat,jprofier)
	
4. 如何监控jvm

   ​	arthas，jstat，jvisualvm等



### 解决JVM运行中问题

1. 一般运维会先收到报警信息

2. 通过top命令查看问题（可能会出现CPU占用居高不下，内存不断增长）

3. top -Hp 1593(进程ID)   观察进程中那些线程CPU和内存占比较高

4. jps 定位到具体的java进程

	jstack 定位线程的状态（WAITING BLOCKDE）状态的信息

	  例子：

	waiting on <0x0000000088ca3310> (a java.lang.Object)
	假如有一个进程中100个线程，很多线程都在waiting on <xx> ，一定要找到是哪个线程持有这把锁
	怎么找？搜索jstack dump的信息，找<xx> ，看哪个线程持有这把锁RUNNABLE

	> 阿里要求线程（线程池）都要写有意义的名字，就是为了在排除问题的时候更容易定位问题
	>
	> **自定义线程池名称，自定义ThreadFactory**

5. jinfo 1593（进程id）： 查看相关java进程信息

6. jstat -gc  可以动态观察GC情况

	**如何定位OOM问题**

	1. **已经上线的 使用top，jstat，jstack等相关命令查询相关信息**
	2. 没有上线的可以用（jconsole等）图形工具（压测等时候）

7. jmap - histo 4655 | head -20，查找4655进程有多少对象产生

8. 设置在OOM自动生成Dump文件（运行jar包时加HeapDumpOnOutOfMemoryError相关参数）,之后用MAT/jhat/jvisualvm 进行dump文件分析

	 https://www.cnblogs.com/baihuitestsoftware/articles/6406271.html 

	```shell
	java -Xms20M -Xmx20M -XX:+UseParallelGC -XX:+HeapDumpOnOutOfMemoryError com.mashibing.jvm.gc.T15_FullGC_Problem01
	```

### JVM案例

1. OOM产生的原因多种多样，有些程序未必产生OOM，不断FGC(CPU飙高，但内存回收特别少) 

	1. 硬件升级系统卡顿（回收算法问题，PS+PO切换为G1垃圾回收）
	2. 线程池运用不当导致OOM问题

2. web项目中设置tomcat http-header-size过大问题

	**在每一个请求都会创建一个指定大小的请求头，当请求过多会导致栈溢出**

3. lambda表达式导致方法区溢出问题（生成的动态代理过多，导致方法区溢出）



## 其他JVM参数

### GC常用参数

* -Xmn -Xms -Xmx -Xss
	年轻代 最小堆 最大堆 栈空间
* -XX:+UseTLAB
	使用TLAB，默认打开
* -XX:+PrintTLAB
	打印TLAB的使用情况
* -XX:TLABSize
	设置TLAB大小
* -XX:+DisableExplictGC
	System.gc()不管用 ，FGC
* -XX:+PrintGC
* -XX:+PrintGCDetails
* -XX:+PrintHeapAtGC
* -XX:+PrintGCTimeStamps
* -XX:+PrintGCApplicationConcurrentTime (低)
	打印应用程序时间
* -XX:+PrintGCApplicationStoppedTime （低）
	打印暂停时长
* -XX:+PrintReferenceGC （重要性低）
	记录回收了多少种不同引用类型的引用
* -verbose:class
	类加载详细过程
* -XX:+PrintVMOptions
* -XX:+PrintFlagsFinal  -XX:+PrintFlagsInitial
	必须会用
* -Xloggc:opt/log/gc.log
* -XX:MaxTenuringThreshold
	升代年龄，最大值15
* 锁自旋次数 -XX:PreBlockSpin 热点代码检测参数-XX:CompileThreshold 逃逸分析 标量替换 ... 
	这些不建议设置

### Parallel常用参数

* -XX:SurvivorRatio
* -XX:PreTenureSizeThreshold
	大对象到底多大
* -XX:MaxTenuringThreshold
* -XX:+ParallelGCThreads
	并行收集器的线程数，同样适用于CMS，一般设为和CPU核数相同
* -XX:+UseAdaptiveSizePolicy
	自动选择各区大小比例

### CMS常用参数

* -XX:+UseConcMarkSweepGC
* -XX:ParallelCMSThreads
	CMS线程数量
* -XX:CMSInitiatingOccupancyFraction
	使用多少比例的老年代后开始CMS收集，默认是68%(近似值)，如果频繁发生SerialOld卡顿，应该调小，（频繁CMS回收）
* -XX:+UseCMSCompactAtFullCollection
	在FGC时进行压缩
* -XX:CMSFullGCsBeforeCompaction
	多少次FGC之后进行压缩
* -XX:+CMSClassUnloadingEnabled
* -XX:CMSInitiatingPermOccupancyFraction
	达到什么比例时进行Perm回收
* GCTimeRatio
	设置GC时间占用程序运行时间的百分比
* -XX:MaxGCPauseMillis
	停顿时间，是一个建议时间，GC会尝试用各种手段达到这个时间，比如减小年轻代

### G1常用参数

* -XX:+UseG1GC
* -XX:MaxGCPauseMillis
	建议值，G1会尝试调整Young区的块数来达到这个值
* -XX:GCPauseIntervalMillis
	？GC的间隔时间
* -XX:+G1HeapRegionSize
	分区大小，建议逐渐增大该值，1 2 4 8 16 32。
	随着size增加，垃圾的存活时间更长，GC间隔更长，但每次GC的时间也会更长
	ZGC做了改进（动态区块大小）
* G1NewSizePercent
	新生代最小比例，默认为5%
* G1MaxNewSizePercent
	新生代最大比例，默认为60%
* GCTimeRatio
	GC时间建议比例，G1会根据这个值调整堆空间
* ConcGCThreads
	线程数量
* InitiatingHeapOccupancyPercent
	启动G1的堆空间占用比例

### 参考资料 

1. [https://blogs.oracle.com/](https://blogs.oracle.com/jonthecollector/our-collectors)[jonthecollector](https://blogs.oracle.com/jonthecollector/our-collectors)[/our-collectors](https://blogs.oracle.com/jonthecollector/our-collectors)
2. https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html
3. http://java.sun.com/javase/technologies/hotspot/vmoptions.jsp
4. JVM调优参考文档：https://docs.oracle.com/en/java/javase/13/gctuning/introduction-garbage-collection-tuning.html#GUID-8A443184-7E07-4B71-9777-4F12947C8184 
5. https://www.cnblogs.com/nxlhero/p/11660854.html 在线排查工具
6. https://www.jianshu.com/p/507f7e0cc3a3 arthas常用命令
7. Arthas手册：
	1. 启动arthas java -jar arthas-boot.jar
	2. 绑定java进程
	3. dashboard命令观察系统整体情况
	4. help 查看帮助
	5. help xx 查看具体命令帮助
8. jmap命令参考： https://www.jianshu.com/p/507f7e0cc3a3 
	1. jmap -heap pid
	2. jmap -histo pid
	3. jmap -clstats pid
