# JVM学习总结

[toc]

##  JVM内存模型

### 对象的创建

### 对象的内存布局

### 对象的访问

### 内存模型分类



## 类加载

### 连接

### 初始化

### 类加载机制

### 加载

## 类卸载

> 一个class要被回收准确的说应该是卸载，必须同时满足以下三个条件
>
> - - 堆中不存在该类的任何实例
>   - 加载该类的classloader已经被回收
>   - 该类的java.lang.Class对象没有在任何地方被引用，也就是说无法通过反射再带访问该类的信息





## JVM  

> Javap -c Math.class  转化成可读的jvm字节码
>
> javap -v Math.class 会有常量池信息



### 为什么不一开始就编译模式？逃逸分析

> 机器码太大了，200mb字节码，有可能就2g多了，所以采用混合模式。
>
> JIT(just in time 及时编译)：热点代码对应的机器码会被缓存起来，下次无需再编译。即时编译过程中JVM会做优化，比如对象逃逸分析。
>
> 逃逸分析：分析对象动态作用域，把对象放到栈里，栈不够才会放到堆上。

###  什么时候进行minor gc？major gc? 

> 都会stop the world 

### 类加载器和双亲委派

> string类的 类加载器是null,因为bootstrap 是c++,java显示是null

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ge6cfhn3hmj30lm04ztck.jpg)

### 各个区域的内容（double）

* 方法区：主要存放类信息，常量池（static常量和static变量），编译后的代码（字节码）等信息
* 堆：初始化的对象，成员变量（那种非static的变量），所有的对象实例和数组都要在堆上分配。
* 栈: 栈的结构是栈桢组成的，调用一个方法就压入一帧，帧上面存储局部变量表，操作数栈，方法出口等信息，局部变量表存放的是8大基础类型加上一个引用类型，所以还是一个指向地址的指针。
* 本地方法栈：主要为Native方法服务。
* 程序计数器：记录当前线程执行的行号。



### GC root 有哪些？

```
（1）虚拟机（JVM）栈中引用对象

（2）方法区中的类静态属性引用对象

（3）方法区中常量引用的对象（final 的常量值）

（4）本地方法栈JNI的引用对象
```



### parallel scavenge 与parnew 区别

```java
两者都是复制算法，都是并行处理，但是不同的是，paralel scavenge 可以设置最大gc停顿时间（-XX:MaxGCPauseMills）以及gc时间占比(-XX:GCTimeRatio)
```

常见OOM（double）

垃圾收集器、算法

类加载机制（double）

### 内存分配并发问题

```
1：对进行分配内存的动作进行同步处理-实际上虚拟机采用CAS配上失败重试的方式保证更新操作的原子性。

2：把内存分配的动作按照线程划分在不同的空间之中进行，即每个线程预先在JAVA堆中预先分配一小块内存，称为本地线程分配缓存（TLAB）。

哪个线程要分配内存，就在哪个线程的TLAB上进行分配。只有在TLAB用完并分配新的TLAB时，才需要加同步锁。虚拟机是否使用TLAB，可以通

过参数-XX:+/UseTLAB参数来设定。

3：直接在栈上分配，如果对象没有发生逃逸，那么对象将不会再堆上分配。对象随线程的销毁而销毁，垃圾回收的时间少，性能高，吞吐量高，响应时间也能提高。
```





### JVM 调优

参数： -XX:PrintFlagsFinal

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ge7ll6yu4aj30il0njgsl.jpg)

* GC收集齐：停顿时间和吞吐量

  * 停顿时间：垃圾收集器进行垃圾回收中断执行相应的时间  （web场景）cms+g1
  * 吞吐量：运行用户代码时间/(运行用户代码时间+垃圾收集时间)  （后台计算交互少）parallel scavege + parallel old
  * 串行收集： Serial Serial old -> 嵌入式设备

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ge7lhdbbq8j30c2095dj0.jpg)

* 内存使用的维度



#### 垃圾选择器选择方案

​    jinfo -flag UseG1GC  pid (查看是否用G1)

* 默认配置 服务器自己去选择
* 小于100mb 配置不行 serial 
* 吞吐量优先 -XX：+UseParalleGC
* 停顿时间：CMS G1

#### 对象分配（调优的基础）

* 15次gc后还存活到老年代

* 大对象到老年代 参数： -XX:PretenureSizeThreshold=1000000 超过1mb的对象

* 对象动态年龄判断：age1+age2+...+agen>50%  直接把age >=n 的放到老年代

* Minor gc后存活的对象Survivor区放不下

* 老年代空间分配担保机制：老年代剩余空间小于新生代所有的对象，如果没有开启那么full gc，开启了的话就判断每次minor gc后进入老年代的平均对象大小，如果满足就minor gc,否则full gc.

  full gc 还不行 就 oom.

#### jps & jinfo  进程和参数

```java
 jps 查看 pid

 jinfo flag pid

 jinfo flag UseG1GC PID
   
 实时修改某个进程中的JVM参数的值： jinf -flag name=value PID {manageable}
```



####  jstat 类和gc状态

```
jstat -class pid 1000 10 // 每1秒输出10次 类加载信息
jstat -gc 60356 1000 10  //  查看
```

#### jstack 查看线程堆栈信息 

```
jstack pid // 如果线程发生问题了，方便排查    查看死锁信息

```

![](https://tva1.sinaimg.cn/large/0082zybply1gc8oa2u9ybj30w70enwtl.jpg)

#### jmap 生成堆内存快照

```
jmap -heap pid  在线看
jmap -dmup:format=b,file=heap.hprof PID
```

2C 4G 生产环境OOM ---> 在发生OOM的时候，能够把堆内存的信息导出来，打印？

         堆信息----> 是什么样的对象占用的内存空间比较大

#### 可视化工具 本质也是对命令行的封装

  下面两个事jdk自带的

* jconsole 

* jvisualvm 

  两者功能有重叠，各有千秋。

* 阿里开源的 arthas

  * 堆使用---OOM---->hprof(自动产生)：

    MAT

  * 垃圾回收：GC日志

    

* MAT  memoryAnalyzer  (可以分析heap.hprof文件) eclipse

  

（1）堆使用----OOM->排查哪个对象占用对象大

           分析 heap文件

（2）垃圾回收  查看日志

         ![](https://tva1.sinaimg.cn/large/0082zybply1gc8r4zskryj30j601x0td.jpg)



![](https://tva1.sinaimg.cn/large/0082zybply1gca67hbkbmj312x0nlk18.jpg)

  

### 垃圾收集算法

* 标记整理        老年代
* 标记清除        老年代
* 复制                年轻代
* 分代收集         老年代+年轻代

### 垃圾收集器

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ge7kswfyrtj30ve0f1q5m.jpg)

* Serial:  新生代：复制算法 ；老年代：标记整理算法 （CMS收集器的备选方案）
* paraNew：多核cpu, serial下的多线程版本。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ge7itpuwi6j30ci07mmyq.jpg)

* parallel scavenge: 关注的是吞吐量

* cms: 并发标记清除 关注的是停顿时间

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ge7j2mos3tj30l207jwhb.jpg)

  * 初始标记
  * 并发标记
  * 重新标记
  * 并发清理

* G1:可以设置一个停顿时间目标

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1ge7ky5mwwmj30w808lta4.jpg)

  * 初始标记
  * 并发标记
  * 最终标记
  * 筛选回收： 对各个region 做个比较，筛选出价值大的



#### G1调优方法

* -XX:+HeapDumpOnOutOfMemoryError  oom的时候会自动dump文件

* 调大堆内存
  * 优点：gc次数变少
  * 缺点: 停顿时间变长
* 设置停顿时间
  * 优点： 停顿时间能够被我们控制（筛选回收，没回收完）
  * 缺点： gc次数增加了

*  堆内存占用比： InitiaingHeapOccupancyPercent  默认45% 可以适当调大

* 其它参数：

  * G1HeapWastePercent: 堆内存浪费比例 
  * ConCGThreads = n 增加回收线程的数量

  

  

  

  ## 博客好文 

  [重启大法好！线上常见问题排查手册](https://mp.weixin.qq.com/s/uajRpzDayZSwTEPhFUoVbQ)

  

  