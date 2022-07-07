# JVM生产环境----性能调优与问题排查

*原文* https://blog.csdn.net/jl19861101/article/details/88368830



**目录**html

[内存调优](http://www.javashuo.com/link?url=#%E5%86%85%E5%AD%98%E8%B0%83%E4%BC%98)java

[1、通用优化](http://www.javashuo.com/link?url=#%E4%B8%80%E3%80%81%E9%80%9A%E7%94%A8%E4%BC%98%E5%8C%96)linux

[2、堆内存调优涉及参数](http://www.javashuo.com/link?url=#%E4%BA%8C%E3%80%81%E5%A0%86%E5%86%85%E5%AD%98%E8%B0%83%E4%BC%98%E6%B6%89%E5%8F%8A%E5%8F%82%E6%95%B0)git

[3、非堆内存调优涉及参数](http://www.javashuo.com/link?url=#%E4%B8%89%E3%80%81%E9%9D%9E%E5%A0%86%E5%86%85%E5%AD%98%E8%B0%83%E4%BC%98%E6%B6%89%E5%8F%8A%E5%8F%82%E6%95%B0)程序员

[GC调优](http://www.javashuo.com/link?url=#GC%E8%B0%83%E4%BC%98)github

[1、通用调优参数](http://www.javashuo.com/link?url=#%E4%B8%80%E3%80%81%E9%80%9A%E7%94%A8%E8%B0%83%E4%BC%98%E5%8F%82%E6%95%B0)web

[2、垃圾收集器的组合策略](http://www.javashuo.com/link?url=#%E4%BA%8C%E3%80%81%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8%E7%9A%84%E7%BB%84%E5%90%88%E7%AD%96%E7%95%A5)算法

[3、各垃圾收集器调优](http://www.javashuo.com/link?url=#%E4%B8%89%E3%80%81%E5%90%84%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8%E8%B0%83%E4%BC%98)数据库

[1. Serial收集器](http://www.javashuo.com/link?url=#1.%20Serial%E6%94%B6%E9%9B%86%E5%99%A8)macos

[2. ParNew收集器](http://www.javashuo.com/link?url=#2.%20ParNew%E6%94%B6%E9%9B%86%E5%99%A8)

[3. Parallel Scavenge收集器](http://www.javashuo.com/link?url=#3.%C2%A0Parallel%20Scavenge%E6%94%B6%E9%9B%86%E5%99%A8)

[4. CMS收集器](http://www.javashuo.com/link?url=#4.%20CMS%E6%94%B6%E9%9B%86%E5%99%A8)

[5. G1收集器](http://www.javashuo.com/link?url=#5.%20G1%E6%94%B6%E9%9B%86%E5%99%A8)

[多线程调优](http://www.javashuo.com/link?url=#%E5%A4%9A%E7%BA%BF%E7%A8%8B%E8%B0%83%E4%BC%98)

[通用调优](http://www.javashuo.com/link?url=#%E9%80%9A%E7%94%A8%E8%B0%83%E4%BC%98)

[锁调优](http://www.javashuo.com/link?url=#%E9%94%81%E8%B0%83%E4%BC%98)

[1. 偏向锁](http://www.javashuo.com/link?url=#1.%20%E5%81%8F%E5%90%91%E9%94%81)

[2.轻量级锁](http://www.javashuo.com/link?url=#2.%E8%BD%BB%E9%87%8F%E7%BA%A7%E9%94%81)

[3.自旋锁](http://www.javashuo.com/link?url=#3.%E8%87%AA%E6%97%8B%E9%94%81)

[4.锁消除](http://www.javashuo.com/link?url=#4.%E9%94%81%E6%B6%88%E9%99%A4)

[5.重量级锁](http://www.javashuo.com/link?url=#5.%E9%87%8D%E9%87%8F%E7%BA%A7%E9%94%81)

[JIT优化](http://www.javashuo.com/link?url=#JIT%E4%BC%98%E5%8C%96)

[调试与问题排查](http://www.javashuo.com/link?url=/#%E8%B0%83%E8%AF%95)

[jvm调试控制参数](http://www.javashuo.com/link?url=#jvm%E8%B0%83%E8%AF%95%E6%8E%A7%E5%88%B6%E5%8F%82%E6%95%B0)

[几种OOM介绍](http://www.javashuo.com/link?url=#%E5%87%A0%E7%A7%8DOOM%E4%BB%8B%E7%BB%8D)

[jvm crash & heap dump & core dump & High CPU](http://www.javashuo.com/link?url=#jvm%20crash%20%26%20heap%20dump%20%26%20core%20dump%20%26%20High%20CPU)

[模拟High CPU----分析](http://www.javashuo.com/link?url=#%E6%A8%A1%E6%8B%9FHigh%20CPU----%E5%88%86%E6%9E%90)

[core dump分析](http://www.javashuo.com/link?url=#core%20dump%E5%88%86%E6%9E%90)

[模拟jvm crash使用CrashAnalysis帮助分析crash 文件，简单例子：](http://www.javashuo.com/link?url=#%E6%A8%A1%E6%8B%9Fjvm%20crash%E4%BD%BF%E7%94%A8CrashAnalysis%E5%B8%AE%E5%8A%A9%E5%88%86%E6%9E%90crash%20%E6%96%87%E4%BB%B6%EF%BC%8C%E7%AE%80%E5%8D%95%E4%BE%8B%E5%AD%90%EF%BC%9A)

[模拟OOM使用eclipse MAT分析heap dump文件，简单例子：](http://www.javashuo.com/link?url=#%E6%A8%A1%E6%8B%9FOOM%E4%BD%BF%E7%94%A8eclipse%20MAT%E5%88%86%E6%9E%90heap%20dump%E6%96%87%E4%BB%B6%EF%BC%8C%E7%AE%80%E5%8D%95%E4%BE%8B%E5%AD%90%EF%BC%9A)

[jdk命令行调试工具](http://www.javashuo.com/link?url=#jdk%E5%91%BD%E4%BB%A4%E8%A1%8C%E8%B0%83%E8%AF%95%E5%B7%A5%E5%85%B7)

[1）jmap -histo:live](http://www.javashuo.com/link?url=#1%EF%BC%89jmap%20-histo%3Alive%20%3Cpid%3E)

[2) jps [options]  [hostid]](http://www.javashuo.com/link?url=#2)%C2%A0jps%20%5Boptions%5D%C2%A0%20%5Bhostid%5D)

[3) jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]](http://www.javashuo.com/link?url=/#3)%20jstat%20-%3Coption%3E%20%5B-t%5D%20%5B-h%3Clines%3E%5D%20%3Cvmid%3E%20%5B%3Cinterval%3E%20%5B%3Ccount%3E%5D)

[4) jmap [option] pid](http://www.javashuo.com/link?url=#4)%C2%A0jmap%20%5Boption%5D%20pid)

[5）jstack [option] pid](http://www.javashuo.com/link?url=#5%EF%BC%89jstack%20%5Boption%5D%20pid)

[调试时的坑](http://www.javashuo.com/link?url=#%E8%B0%83%E8%AF%95%E6%97%B6%E7%9A%84%E5%9D%91)

[linux命令行调试工具](http://www.javashuo.com/link?url=#linux%E5%91%BD%E4%BB%A4%E8%A1%8C%E8%B0%83%E8%AF%95%E5%B7%A5%E5%85%B7)

[top](http://www.javashuo.com/link?url=#top)

[pmap](http://www.javashuo.com/link?url=#pmap%20%3Cpid%3E)

[BTrace监控调试](http://www.javashuo.com/link?url=#BTrace%E7%9B%91%E6%8E%A7%E8%B0%83%E8%AF%95)

[描述](http://www.javashuo.com/link?url=#%E6%8F%8F%E8%BF%B0)

[支持jdk11](http://www.javashuo.com/link?url=#%E6%94%AF%E6%8C%81jdk11)

[jdk11小测试](http://www.javashuo.com/link?url=#jdk11%E5%B0%8F%E6%B5%8B%E8%AF%95)

[webBTraceUtil小工具](http://www.javashuo.com/link?url=#webBTraceUtil%E5%B0%8F%E5%B7%A5%E5%85%B7)

[BTrace总结：](http://www.javashuo.com/link?url=#BTrace%E6%80%BB%E7%BB%93%EF%BC%9A)

[可视化的GC日志分析](http://www.javashuo.com/link?url=#%E5%8F%AF%E8%A7%86%E5%8C%96%E7%9A%84GC%E6%97%A5%E5%BF%97%E5%88%86%E6%9E%90)

[GCEasy](http://www.javashuo.com/link?url=#GCEasy)

[GCViewer](http://www.javashuo.com/link?url=#GCViewer)

[图形界面性能监控工具](http://www.javashuo.com/link?url=#%E5%9B%BE%E5%BD%A2%E7%95%8C%E9%9D%A2%E6%80%A7%E8%83%BD%E7%9B%91%E6%8E%A7%E5%B7%A5%E5%85%B7)

[开启远程监控-JMX](http://www.javashuo.com/link?url=#%E5%BC%80%E5%90%AF%E8%BF%9C%E7%A8%8B%E7%9B%91%E6%8E%A7-JMX)

[开启远程监控-jstatd](http://www.javashuo.com/link?url=#%E5%BC%80%E5%90%AF%E8%BF%9C%E7%A8%8B%E7%9B%91%E6%8E%A7-jstatd)

[VisualVM](http://www.javashuo.com/link?url=#VisualVM)

[JDK Mission Control 7.1.0](http://www.javashuo.com/link?url=#JDK%20Mission%20Control%207.1.0)

[JProfile](http://www.javashuo.com/link?url=#JProfile)

[总结](http://www.javashuo.com/link?url=#%E6%80%BB%E7%BB%93)

[JVM性能调优思路](http://www.javashuo.com/link?url=#JVM%E6%80%A7%E8%83%BD%E8%B0%83%E4%BC%98%E6%80%9D%E8%B7%AF)

[JVM生产环境问题排查思路](http://www.javashuo.com/link?url=#JVM%E7%94%9F%E4%BA%A7%E7%8E%AF%E5%A2%83%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5%E6%80%9D%E8%B7%AF)

---

        JVM性能调优是一个长期的按部就班的过程，是一个有深度且有广度的课题。不一样的应用场景适用于不一样的优化方案，本文只经过内存优化、GC垃圾收集器优化、线程调优、JIT编译器调优等四个方向提供一些相关的优化方法。而且会持续的丰富下去。若有错误的地方，很是但愿可以一块儿探讨、学习、进步。本文只针对JDK8及后续版本，默认运行在server模式下。本文仅限于Oracle hotspot的jvm层面调优和生产环境的问题排查，并不会涉及到代码层面编写的各类工做。

        注：阅读本文前须要对JMM、OOM、GC收集器以及GC收集算法、多线程与锁或无锁CAS(这里的线程都是用户线程，但能够帮助区分和理解本文中所说的工做线程也就是JVM工做线程)、JIT编译器的运做方式有必定的了解。

> **JMM参考资料：**
> 
> [全面理解Java内存模型(JMM)及volatile关键字 - JavaShuo](http://www.javashuo.com/article/p-rnfjmtel-co.html)
> 
> **OOM参考资料：**
> 
> [https://blog.csdn.net/sunquan291/article/details/79109197](http://www.javashuo.com/link?url=https://blog.csdn.net/sunquan291/article/details/79109197)
> 
> **GC收集器参考资料：**
> 
> [https://blog.csdn.net/jl19861101/article/details/88169405](http://www.javashuo.com/link?url=https://blog.csdn.net/jl19861101/article/details/88169405)
> 
> [https://blog.csdn.net/jl19861101/article/details/88566316](http://www.javashuo.com/link?url=https://blog.csdn.net/jl19861101/article/details/88566316)
> 
> **多线程参考资料：**
> 
> [https://blog.csdn.net/jl19861101/article/details/87357979](http://www.javashuo.com/link?url=https://blog.csdn.net/jl19861101/article/details/87357979)
> 
> [深刻理解Java并发之synchronized实现原理 - JavaShuo](http://www.javashuo.com/article/p-uvajwpbz-mo.html)
> 
> [Java并发编程-无锁CAS与Unsafe类及其并发包Atomic - JavaShuo](http://www.javashuo.com/article/p-uvunpvss-bo.html)
> 
> [深刻剖析基于并发AQS的(独占锁)重入锁(ReetrantLock)及其Condition实现原理 - JavaShuo](http://www.javashuo.com/article/p-gymdvscb-bh.html)
> 
> **JIT参考资料：**
> 
> [https://blog.csdn.net/jl19861101/article/details/88667402](http://www.javashuo.com/link?url=https://blog.csdn.net/jl19861101/article/details/88667402)
> 
> [晚期（运行期）优化 - JavaShuo](http://www.javashuo.com/article/p-phcqgfym-a.html)
> 
> ----------------------------------------------------------------------------------
> 
> 或参考《深刻理解Java虚拟机_JVM高级特性与最佳实践 第2版》《实战Java虚拟机：JVM故障诊断与性能优化》

# 内存调优

内存调优分为堆内存调优与非堆内存调优

jvm堆内存分配示意图：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/879_ce5_21b.jpg)

对象内存分配流程：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/880_aea_761.jpg)

经过命令jhsdb jmap --heap --pid <pid>或者jmap -heap <pid>查看heap状况

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/881_ec5_f48.png)

## 1、通用优化

- -XX:-RestrictContended，开关参数。默认开启。关闭此参数能够消除伪共享参数，但须要配合@sun.misc.Contended注解才会生效。
- -XX:+UseCompressedOops，开关参数。默认开启。该参数指定是否启用指针压缩优化方式。指针压缩优化方式能够减少内存占用。
- -XX:-UseLargePages，开关参数。默认关闭。该参数指定是否支持大内存页优化。

> 测试linux是否支持largepage：  
> 
> # cat /proc/meminfo | grep Huge
> 
> HugePages_Total: 0  
> HugePages_Free: 0  
> Hugepagesize: 2048 kB  
> 若是有HugePage字样的输出内容，说明OS是支持大内存分页的。Hugepagesize就是默认的大内存页size。接下来，为了让JVM能够调整大内存页size，须要设置下OS 共享内存段最大值和大内存页数量。  
> 共享内存段最大值，建议这个值大于Java Heap size，这个例子里设置了4G内存。  
> $ echo 4294967295 > /proc/sys/kernel/shmmax  
> 大内存页数量,这个值通常是 Java进程占用最大内存/单个页的大小，好比java设置1.5G，单个页10M，那么数量为1536/10 = 154。  
> $ echo 154 > /proc/sys/vm/nr_hugepages

- -XX:LargePageSizeInBytes，数值参数。该参数指定单个页大小。

## 2、堆内存调优涉及参数

- -Xmx:参数设置JVM最大可用堆（单位k,m,g）。表示java虚拟机堆区内存可被分配的最大上限，一般为操做系统可用内存的1/4大小。可是开发过程当中，一般会将 -Xms 与 -Xmx两个参数的配置相同的值，其目的是为了可以在java垃圾回收机制清理完堆区后不须要从新分隔计算堆区的大小而浪费资源。
- -Xms:参数设置JVM初始堆大小（单位k,m,g）。表示java虚拟机堆区内存初始内存分配的大小，一般为操做系统可用内存的1/64大小便可，但仍需按照实际状况进行分配。有可能真的按照这样的一个规则分配时，设计出的软件尚未可以运行得起来就挂了。
- -Xmn:新生代大小（单位k,m,g）。能够经过这个值获得老年代大小：-Xmx减去-Xmn。该参数设置之后-XX:NewSize、-XX:MaxNewSize:都会等于该值。
- -XX:NewSize:初始新生代大小。
- -XX:MaxNewSize:最大新生代大小。
- -XX:NewRatio:设置年轻代（包括Eden和两个Survivor区）与年老代的比值（除去持久代）。等同于设置-XX:MaxNewSize。
- -XX:SurvivorRatio:Eden/Survivor的值. 这个值的说明, 不少网上转载的都是错的. 8表示Survivor:Eden=1:8, 由于survivor区有2个, 因此Eden的占比为8/10. Ratio of eden/survivor space size. -XX:SurvivorRatio=6 sets the ratio between each survivor space and eden to be 1:6, each survivor space will be one eighth of the young generation. 
- -XX:MaxHeapFreeRatio:GC后若是发现空闲堆内存占到整个预估堆内存的N%(百分比)，则收缩堆内存的预估最大值, 预估堆内存是堆大小动态调控的重要选项之一. 堆内存预估最大值必定小于或等于固定最大值(-Xmx指定的数值). 前者会根据使用状况动态调大或缩小, 以提升GC回收的效率
- -XX:MinHeapFreeRatio:GC后若是发现空闲堆内存占到整个预估堆内存的N%(百分比), 则放大堆内存的预估最大值
- -XX:MaxTenuringThreshold:默认值15。用来设置多少次Minor GC后对象会进入Old区域。晋升年龄最大阈值，默认15。在新生代中对象存活次数(通过YGC的次数)后仍然存活，就会晋升到老年代。每通过一次YGC，年龄加1，当survivor区的对象年龄达到TenuringThreshold时，表示该对象是长存活对象，就会直接晋升到老年代。
- -XX:TargetSurvivorRatio:设定survivor区的目标使用率。默认50，即survivor区对象目标使用率为50%。
- -XX:+UseTLAB:开关参数。启动TLAB。默认启动。
- -XX:TLABWasteTargetPercent:设置TLAB空间所占用Eden空间的百分比大小。 
- -XX:TLABRefillWasteFraction:设置维护进入TLAB空间单个对象大小，比例值，默认1/64，对象大于该值会去堆建立。
- -XX:+ResizeTLAB:开关参数，默认开启。默认状况下TLAB和RefillWaste大小会在运行时不断调整的，使系统的运行状态达到最优。若是想要禁用自动调整TLAB的大小，可使用-XX:-ResizeTLAB禁用ResizeTLAB，并使用-XX:TLABSize手工指定一个TLAB的大小。
- -XX:TLABSize:手动指定TLAB大小。默认0。
- -XX:MinTLABSize:最小TLAB大小。默认2048。
- -XX:-AlwaysPreTouch:开关参数。默认关闭。启动时就把参数里说好了的内存所有舔一遍，可能令得启动时慢上一点，但后面访问时会更流畅，好比页面会连续分配，好比不会在晋升新生代到老生代时才去访问页面使得GC停顿时间加长。不过这选项对大堆才会更有感受一点。（参考资料：[https://docs.oracle.com/javase/9/gctuning/garbage-first-garbage-collector-tuning.htm#JSGCT-GUID-4914A8D4-DE41-4250-B68E-816B58D4E278](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/9/gctuning/garbage-first-garbage-collector-tuning.htm#JSGCT-GUID-4914A8D4-DE41-4250-B68E-816B58D4E278)）
- -XX:YoungGenerationSizeIncrement数值参数，默认20。虚拟机动态调全年轻代增长比率。默认20%。
- -XX:TenuredGenerationSizeIncrement数值参数，默认20。虚拟机动态调整老年代的增长比率。默认20%。
- -XX:AdaptiveSizeDecrementScaleFactor数值参数，默认4。虚拟机动态调整空间的收缩比率。若是增加比率是20%。那么收缩比率=20/4=5%。那么空间收缩比率为5%。
- -XX:SoftRefLRUPolicyMSPerMB数值参数，默认1000。单位ms。每1M空闲空间可保持SoftReference对象生存的时长。

> 注意：JVM会将每一个对象的年龄信息、各个年龄段对象的总大小记录在“age table”表中。基于“age table”、survivor区大小、survivor区目标使用率（-XX:TargetSurvivorRatio）、晋升年龄阈值（-XX:MaxTenuringThreshold），JVM会动态的计算tenuring threshold的值。一旦对象年龄达到了tenuring threshold就会晋升到老年代。
> 
>  为何要动态的计算tenuring threshold的值呢？假设有不少年龄还未达到TenuringThreshold的对象依旧停留在survivor区，这样不利于新对象从eden晋升到survivor。所以设置survivor区的目标使用率，当使用率达到时从新调整TenuringThreshold值，让对象尽早的去old区。  
> 内容来源：[https://blog.csdn.net/zero__007/article/details/52797684](http://www.javashuo.com/link?url=https://blog.csdn.net/zero__007/article/details/52797684)
> 
> 能够参考：[https://blog.csdn.net/foolishandstupid/article/details/77596050](http://www.javashuo.com/link?url=https://blog.csdn.net/foolishandstupid/article/details/77596050)

## 3、非堆内存调优涉及参数

- -Xss:设置每一个用户线程的堆栈大小。
- -XX:+EliminateAllocations 启用标量替换，容许对象打散分配到栈上。此参数须要配合DoEscapeAnalysis参数使用。
- -XX:AutoBoxCacheMax=128。默认值128。Integer i = 3;这语句有着 int自动装箱成Integer的过程，JDK默认只缓存 -128 ~ +127的int 和 long，超出范围的数字就要即时构建新的Integer对象。
- -XX:MetaspaceSize:分配给类元数据空间的初始大小(Oracle逻辑存储上的初始高水位，the initial high-water-mark ). 此值为估计值. MetaspaceSize设置得过大会延长垃圾回收时间. 垃圾回收事后, 引发下一次垃圾回收的类元数据空间的大小可能会变大
- -XX:MaxMetaspaceSize:是分配给类元数据空间的最大值, 超过此值就会触发Full GC. 此值仅受限于系统内存的大小, JVM会动态地改变此值
- -XX:MinMetaspaceFreeRatio:默认值40。表示一次GC之后，为了不增长元数据空间的大小，空闲的类元数据的容量的最小比例，不够就会致使垃圾回收。
- -XX:MaxMetaspaceFreeRatio:默认值70。表示一次GC之后，为了不增长元数据空间的大小，空闲的类元数据的容量的最大比例，不够就会致使垃圾回收。
- -XX:InitialCodeCacheSize:初始JIT编译器本地机器码缓存区大小。初始化大小不该该小于系统最小内存页大小。
- -XX:ReservedCodeCacheSize:（单位k,m,g）默认240M，若是使用-XX:-TieredCompilation参数禁用分层编译，则默认大小为48M。本地机器码缓冲区大小。JIT编译器编译后的机器码保存在内存中，他的大小由此参数控制。一旦代码缓存耗尽，JIT就会中止，而且在整个虚拟机的声明周期中，不会再启动了。这个参数最大可设置2GB，超过此限制将会产生错误（Invalid ReservedCodeCacheSize=2049M. Must be at most 2048M.Error: Could not create the Java Virtual Machine.Error: A fatal exception has occurred. Program will exit.）。
- -XX:SegmentedCodeCache:开关参数，默认开启。JDK9提供的本地代码缓冲区分段缓存功能。和原来的单一缓存区域不一样的是, 新的代码缓存根据代码自身的生命周期而分为三种:永驻代码(non-method code),短时间代码(profiled code),长期代码(Non-profiled code)。缓存分段会在各个方面提高程序的性能, 好比作垃圾回收扫描的时候能够直接跳过非方法代码(永驻代码), 从而提高效率。
- -XX:MaxDirectMemorySize:直接内存最大值。即NIO进行操做时，能够分配的最大缓存值，默认和heap最大值一致。
- -XX:CompressedClassSpaceSize:类指针压缩空间大小, 默认为1G

> 注意：如下结论在JDK11.0.2_LTS版本中测试所得。其他版本并无测试过。因此不一样的版本也有可能会有不一样的结论。
> 
> 1）参数设置存在前后顺序。以-Xmn、-XX:NewSize、-XX:MaxNewSize为例。
> 
> -Xmn等同于-XX:NewSize、-XX:MaxNewSize的设置。若是设置了-Xmn的值，NewSize与MaxNewSize的值都会等于该值。可是若是三个参数同时设置了就会致使少量的混乱。jvm会按照参数编写的前后顺序设置系统值。例如参数编写顺序为：-XX:MaxNewSize=256m  -Xmn512m  -XX:NewSize=256m。那么获得的将会是：
> 
> ![](http://static.javashuo.com/static/loading.gif)
> 
> 因为先设置的MaxNewSize的值倍Xmn的值覆盖，因此MaxNewSize等于512m。因为最后设置了NewSize也就是初始新生代大小，因此最终只改变了NewSize的值。
> 
> 2）参数设置存在优先级问题。以-XX:NewSize、-XX:MaxNewSize、-Xmn与--XX:NewRatio为例。NewSize、MaxNewSize、Xmn的优先级高于RewRatio的设置。若是前三个参数其中任何一个与NewRatio同时存在于一个同一个jvm进程启动参数中的话。NewRatio设置参数值将会失效。
> 
> 因此jvm启动参数请不要混乱使用，须对各参数理解其功能特性，不然会致使后续或者其余jvm调优人员解读起来产生歧义或产生错误的判断。

# GC调优

## 1、通用调优参数

- -XX:+DisableExplicitGC 开关参数，开启此参数会禁用代码中的System.gc()，因为该方法默认会触发 FGC，而且忽略参数中的 UseG1GC 和 UseConcMarkSweepGC，所以必要时能够禁用该方法。
- -XX:+ExplicitGCInvokesConcurrent 开启此该参数能够改变代码中System.gc()的行为，也就是说，System.gc() 后不使用 FGC ，而是使用配置的并发收集器进行并发收集。注意：使用此选项就不要 使用DisableExplicitGC参数。
- -XX:PretenureSizeThreshold=n 除了年龄外，对象的体积也是影响晋升的一个关键，也就是大对象。若是一个对象新生代放不下，只能直接经过分配担保机制进入老年代。该参数是设置对象直接晋升到老年代的阈值，单位是字节。只要对象的大小大于此阈值，就会直接绕过新生代，直接进入老年代。注意：若是想使这个参数生效，请关闭UseAdaptiveSizePolicy参数。
- -XX:+ScavengeBeforeFullGC开关参数。在执行Full GC以前自己不会进行Minor GC，咱们能够配置-XX:+ScavengeBeforeFullGC，让jvm进行Full GC以前先进行一次Minor GC。
- -XX:ParGCCardsPerStrideChunk=256 该参数控制GC工做线程的任务粒度。在Java中，一个card的大小是512 bytes，而ParGCCardsPerStrideChunk默认的值是256。那么一个Stride，或者MSDN文章中说的block，就是512 * 256 = 128K。假如你有4G的old gen，就会有4G ／ 128K = 32K的Strides在young gen collection的时候去扫描。这也就是为何我前面说LinkedIn文章的interesting有些大惊小怪，呃，算法原本就是这个样子的啊。随着old gen大小增长，young gen collection就是要作更多的事情。ParGCCardsPerStrideChunk设置成多大，这个并非仅仅由old gen的大小决定的，更大的决定因素是你的系统。有的文章也给出了32768的值，还有待验证。（该参数为诊断参数，须要-XX:+UnlockDiagnosticVMOptions参数来打开此隐藏参数）
- -XX:+UseGCOverheadLimit，开关参数。默认启动。若是在垃圾收集（GC）中花费的时间太多，则垃圾收集器将抛出OutOfMemoryError：若是在垃圾收集中花费的总时间超过98%，而且回收的堆少于2%，则将抛出OutOfMemoryError。
- -XX:UseAdaptiveSizePolicy，开关参数。默认开启。开关打开后就不须要手工指定新生代的大小(-Xmm)、Eden与Servivor区的比例(-XX:SurvivorRatio)、晋升老年代对象年龄(-XX:PretenureSizeThreshold)等细节参数。虚拟机会根据当前系统的运行状况收集性能监控信息，动态调整这些参数以提供最合适的停顿时间或者最大的吞吐量，这种调节方式成为GC自适应的调剂策略。这也是Parallel Scavenge收集器与ParNew收集器的一个重要区别。
- -XX:ParallelRefProcEnabled，开关参数。该参数指定是否并行处理Reference对象。

## 2、垃圾收集器的组合策略

- -XX:+UseSerialGC参数可指定新生代Serial收集器，老年代Serial Old收集器。
- -XX:+UseParNewGC:新生代使用ParNew收集器，老年代使用Serial Old收集器。(请不要在JDK1.8版本后续使用此参数)
- -XX:+UseConcMarkSweepGC:新生代使用ParNew收集器，老年代使用CMS收集器。
- -XX:+UseParallelGC:新生代使用ParallelGC收集器，老年代使用Serial Old收集器。
- -XX:+UseParallelOldGC:新生代使用ParallelGC收集器，老年代使用ParallelOldGC收集器。
- -XX:+UseG1GC：开关参数。用来开启和关闭G1收集器。
- -XX:+UnlockExperimentalVMOptions -XX:+UseZGC：参数组合可开启ZGC垃圾收集器。（注意：此为JDK11开始提供参数。目前ZGC是实验版本，并且只适合在Linux环境下使用）

## 3、各垃圾收集器调优

### 1. Serial收集器

截止目前JDK11版本。目前没有相关调优参数

### 2. ParNew收集器

- -XX:ParallelGCThreads={value} 这个参数是指定STW工做线程的数量，通常最好和 CPU 核心数量至关。默认状况下，当 CPU 数量小于8， ParallelGCThreads 的值等于 CPU 数量，当 CPU 数量大于 8 时，则使用公式：3+（（5*CPU）/ 8）；同时这个参数只要是并行 GC 均可以使用，不仅是 ParNew。

### 3. Parallel Scavenge收集器

- -XX:ParallelGCThreads={value} 这个参数是指定STW工做线程的数量，通常最好和 CPU 核心数量至关。默认状况下，当 CPU 数量小于8， ParallelGCThreads 的值等于 CPU 数量，当 CPU 数量大于 8 时，则使用公式：3+（（5*CPU）/ 8）；同时这个参数只要是并行 GC 均可以使用。
- -XX:MaxGCPauseMillis 设置最大垃圾收集停顿时间，他的值是一个大于0的整数。ParallelGC 工做时，会调整 Java 堆大小或者其余的一些参数，尽量的把停顿时间控制在 MaxGCPauseMillis 之内。若是为了将停顿时间设置的很小，将此值也设置的很小，那么 Parallel Scavenge 将会把堆设置的也很小，这将会致使频繁 GC ，虽然系统停顿时间小了，但总吞吐量降低了。
- -XX:GCTimeRatio 设置吞吐量大小，他的值是一个0 到100之间的整数，假设 GCTimeRatio 的值是 n ，那么系统将花费不超过 1/(1+n) 的时间用于垃圾收集，即不超过1% 的时间用于垃圾收集。

### 4. CMS收集器

- -XX:ConcGCThreads=n 设置并发标记线程数。将n设置为大约并行垃圾收集线程数（ParallelGCThreads）的1/4。(注意：网上其余文章把此参数定义为与ParallelGCThreads一样的设置结果是错误的。参考资料：[https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html)与[https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE](http://www.javashuo.com/link?url=https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE)与[https://www.oracle.com/technetwork/cn/articles/java/g1gc-1984535-zhs.html](http://www.javashuo.com/link?url=https://www.oracle.com/technetwork/cn/articles/java/g1gc-1984535-zhs.html)。官方对于两个参数给出的是不一样的解释。也多是个人理解有误，此结论仅仅只是在官方文档中找到的答案，但并未在openjdk源码中获得证明)
- -XX:ParallelGCThreads={value} 这个参数是指定STW工做线程的数量，通常最好和 CPU 核心数量至关。默认状况下，当 CPU 数量小于8， ParallelGCThreads 的值等于 CPU 数量，当 CPU 数量大于 8 时，则使用公式：3+（（5*CPU）/ 8）；同时这个参数只要是并行 GC 均可以使用。
- -XX:+CMSPrecleaningEnabled,CMS预清理阶段开关参数。
- -XX:CMSInitiatingOccupancyFraction是一个0~100之间的整数。用来标记老年代使用比例达到某个比例时触发CMS垃圾收集器。JDK1.5默认值68。表示老年代使用空间达到68%时CMS垃圾收集器会被激活。若是老年代内存增加很快，建议下降阈值，避免 FGC，若是增加慢，则能够加大阈值，减小 CMS GC 次数。提升吞吐量。若是该值设置为-1的话，则由另外两个参数决定启动CMS收集器MinHeapFreeRatio、CMSTriggerRatio。((100 - MinHeapFreeRatio) + (double)( CMSTriggerRatio * MinHeapFreeRatio) / 100.0) / 100.0 。
- -XX:+UseCMSCompactAtFullCollection 因为 CMS 使用标记清理算法，内存碎片没法避免。该参数指定每次 CMS 后进行一次碎片整理。（JDK8还在使用JDK9开始删除此参数）
- -XX:CMSFullGCsBeforeCompaction 因为每次进行碎片整理将会影响性能，你可使用该参数设定多少次 CMS 后才进行一次碎片整理，也就是内存压缩。（JDK8还在使用JDK9开始删除此参数）
- -XX:+CMSClassUnloadingEnabled 容许对类元数(旧版本JVM中的永久代，JDK8版本之后的Metaspace区域)据进行回收。它会增长CMS remark的暂停时间，因此若是新类加载并不频繁，这个参数仍是不开的好。
- -XX:+UseCMSInitiatingOccupancyOnly 开启此参数会关闭CMS自动计算垃圾收集机制。而且彻底按照CMSInitiatingOccupancyFraction设置的值来启动CMS收集器。若是关闭此参数，CMSInitiatingOccupancyFraction的值则会变成一个参考值。
- -XX:CMSWaitDuration 数值参数，单位毫秒，默认值2000。此参数定义间隔N毫秒检测一次是否须要启动CMS垃圾收集。
- -XX:-CMSScavengeBeforeRemark开关参数。开启或关闭在CMS从新标记阶段以前的清除（YGC）尝试，CMS并发标记阶段与用户线程并发进行，此阶段会产生已经被标记了的对象又发生变化的状况，若打开此开关，可在必定程度上下降CMS从新标记阶段对上述“又发生变化”对象的扫描时间，固然，“清除尝试”也会消耗一些时间。

### 5. G1收集器

G1收集器的详细介绍请参阅：[JVM Garbage First(G1)](http://www.javashuo.com/link?url=https://blog.csdn.net/jl19861101/article/details/88566316)

**调优参数：**

- -XX:GCTimeRatio 设置吞吐量大小，他的值是一个0 到100之间的整数，假设 GCTimeRatio 的值是 n ，那么系统将花费不超过 1/(1+n) 的时间用于垃圾收集，即不超过1% 的时间用于垃圾收集。
- -XX:MaxGCPauseMillis=200默认值200毫秒。用于指定最大停顿时间，若是任何一次停顿超过这个设置值时，G1 就会尝试调整新生代和老年代的比例，调整堆大小，调整晋升年龄的手段，试图达到目标。和 PS 同样，停顿时间小了，对应的吞吐量也会变小。这点值得注意。使用MaxGCPauseMillis设置最大停顿时间，G1垃圾收集器使用一个预测模型来决定多少垃圾收集工做能够在最大停顿时间内完成。在垃圾收集工做完成的时候G1自动选择下一个须要垃圾收集目标也就是收集集合(Collect Set)。(官方给出的解释是，为了使G1可以尽最大可能的保证该参数最大停顿时间的工做性能，尽可能不要手动设置新生代大小)
- -XX:GCPauseIntervalMillis 设置STW的间隔时间。
- -XX:G1HeapRegionSize=n 当使用G1收集器时，设置一个region区域的大小。这个大小范围在1M到32M之间，必须是2的次方。
- -XX:G1NewSizePercent=5 年轻代在堆中最小百分比，默认值是 5%（这是一个实验性参数experimental，若是使用须要附加参数-XX:+UnlockExperimentalVMOptions）
- -XX:G1MaxNewSizePercent=60 年轻代在堆中的最大百分比，默认值是 60%（这是一个实验性参数experimental，若是使用须要附加参数-XX:+UnlockExperimentalVMOptions）
- -XX:ParallelGCThreads={value} 这个参数是指定STW工做线程的数量，通常最好和 CPU 核心数量至关。默认状况下，当 CPU 数量小于8， ParallelGCThreads 的值等于 CPU 数量，当 CPU 数量大于 8 时，则使用公式：3+（（5*CPU）/ 8）；同时这个参数只要是并行 GC 均可以使用。
- -XX:ConcGCThreads=n 设置并发标记线程数。将n设置为大约并行垃圾收集线程数（ParallelGCThreads）的1/4。
- -XX:InitiatingHeapOccupancyPercent=45，数值参数。该参数决定了IHOP的初始值，IHOP能够决定当整个堆使用率达到多少时，触发并发标记阶段的执行。默认值时45，即当堆的使用率达到45%，执行并发标记阶段，该值一旦设置，始终都不会被 G1修改。也就是说，G1 就算为了知足 MaxGCPauseMillis 也不会修改此值。若是该值设置的很大，致使并发周期迟迟得不到启动，那么引发 FGC 的概率将会变大。若是太小，则会频繁标记，GC 线程抢占应用程序CPU 资源，性能将会降低。
- -XX:G1MixedGCLiveThresholdPercent=85 为混合垃圾回收周期中要包括的老年代区域设置占用率阈值。默认占用率为 85%。（这是一个实验性参数experimental，若是使用须要附加参数-XX:+UnlockExperimentalVMOptions）
- -XX:G1HeapWastePercent=5 该参数设置您愿意浪费的堆百分比。若是可回收百分比小于堆废物百分比，Java HotSpot VM 不会启动混合垃圾回收周期。
- -XX:G1MixedGCCountTarget=8 该参数设置标记周期完成后，对存活数据上限为 `G1MixedGCLIveThresholdPercent` 的旧区域执行混合垃圾回收的目标次数。默认值是 8 次混合垃圾回收。混合回收的目标是要控制在此目标次数之内。
- -XX:G1OldCSetRegionThresholdPercent=10 该参数设置一个混合收集周期回收老年区域的回收上限。默认值是 Java 堆的 10%。（这是一个实验性参数experimental，若是使用须要附加参数-XX:+UnlockExperimentalVMOptions）
- -XX:G1ReservePercent=10 设置做为空闲空间的预留内存百分比，以下降目标空间溢出的风险。默认值是 10%。增长或减小百分比时，请确保对总的 Java 堆调整相同的量。
- -XX:G1RSetUpdatingPauseTimePercent=10 改参数表明G1修改RSet时间百分比。默认是目标停顿时间的10%。
- -XX:+G1EagerReclaimHumongousObjects，开关参数。默认开启。该参数指示了回收大对象（Humongous Objects）的时机。开启该参数，虚拟机会在任何STW的适当时候回收大对象。关闭该参数，虚拟机只会在Cleanup pause后与Full GC期间回收大对象。
- -XX:+G1UseAdaptiveIHOP，开关参数。默认开启。该参数表示是否启动自适应IHOP。若是启动自适应IHOP，虚拟机会在运行时自动调整IHOP值，也就是启动初始标记的阈值。
- -XX:+ReduceInitialCardMarks，开关参数。默认开启。分配对象时候的一个优化参数。在G1下若是分配较多的大对象。极可能形成停顿时间加长。能够尝试禁用此选项。慎用。
- -XX:G1RSetRegionEntries，数值参数。增大这个值能够有效的减小RSet的压缩coarsening的发生概率。
- -XX:ReferencesPerThread，数值参数。该参数决定了并发度：每N个Reference对象都会有一个线程参与Reference处理，这个值受限于-XX:ParallelGCThreads。若是设置为0,标明最大线程数老是-XX:ParallelGCThreads。这个决定了对 java.lang.Ref.* 这些对象实例的处理是不是多个线程并行执行的。

**建议：**

评估和微调 G1 GC 时，请记住如下建议：

- **年轻代大小**：避免使用 `-Xmn` 选项或 `-XX:NewRatio` 等其余相关选项手动设置年轻代大小。固定年轻代的大小会覆盖暂停时间目标(MaxGCPauseMillis)。
- **暂停时间目标**：每当对垃圾回收进行评估或调优时，都会涉及到延迟与吞吐量的权衡。G1 GC 是增量垃圾回收器，暂停统一，同时应用程序线程的开销也更多。G1 GC 的吞吐量目标是 90% 的应用程序时间和 10%的垃圾回收时间。若是将其与 Java HotSpot VM 的吞吐量回收器相比较，目标则是 99% 的应用程序时间和 1% 的垃圾回收时间。所以，当您评估 G1 GC 的吞吐量时，暂停时间目标不要太严苛。目标太过严苛表示您愿意承受更多的垃圾回收开销，而这会直接影响到吞吐量。当您评估 G1 GC 的延迟时，请设置所需的（软）实时目标，G1 GC 会尽可能知足。反作用是，吞吐量可能会受到影响。
- **掌握混合垃圾回收**：当您调优混合垃圾回收时，请尝试如下选项。  
   
  - `-XX:InitiatingHeapOccupancyPercent`  
    用于更改标记阈值。
  - `-XX:G1MixedGCLiveThresholdPercent` 和 `-XX:G1HeapWastePercent`  
    当您想要更改混合垃圾回收决定时。
  - `-XX:G1MixedGCCountTarget` 和 `-XX:G1OldCSetRegionThresholdPercent`  
    当您想要调整旧区域的 CSet 时。

# 多线程调优

## 通用调优

- -XX:ThreadStackSize 设置每一个用户线程的堆栈大小。该参数与-Xss参数的设置效果是同样的。若是两个参数设置为0，则虚拟机自动设置为默认值，(此为各不一样平台默认值：Linux/ARM (32-bit): 320 KB、Linux/i386 (32-bit): 320 KB、Linux/x64 (64-bit): 1024 KB、OS X (64-bit): 1024 KB、Oracle Solaris/i386 (32-bit): 320 KB、Oracle Solaris/x64 (64-bit): 1024 KB)
- -XX:ParallelGCThreads={value} 这个参数是垃圾收集器STW工做线程的数量，通常最好和 CPU 核心数量至关。默认状况下，当 CPU 数量小于8， ParallelGCThreads 的值等于 CPU 数量，当 CPU 数量大于 8 时，则使用公式：3+（（5*CPU）/ 8）；同时这个参数只要是并行 GC 均可以使用。
- -XX:ConcGCThreads=n 垃圾收集器并发标记阶段线程数。将n设置为大约并行垃圾收集线程数（ParallelGCThreads）的1/4。

注意：下面参数中英文资料太少。只找到下面这些不多的资料。但能够肯定与GC垃圾收集工做线程有关系。具体的相关实现须要进一步查证。(中文资料[https://www.cnblogs.com/leonxyzh/p/7484212.html](http://www.javashuo.com/link?url=https://www.cnblogs.com/leonxyzh/p/7484212.html) ，[https://www.jianshu.com/p/debef4f69a90](http://www.javashuo.com/link?url=https://www.jianshu.com/p/debef4f69a90)英文资料[https://engineering.linkedin.com/garbage-collection/garbage-collection-optimization-high-throughput-and-low-latency-java-applications](http://www.javashuo.com/link?url=https://engineering.linkedin.com/garbage-collection/garbage-collection-optimization-high-throughput-and-low-latency-java-applications) 资料来源并不是官方文档，而是LinkedIn在产品迭代中的实践结论)

- -XX:VMThreadStackSize 虚拟机内部线程大小。好比说STW线程或Concurrent Mark线程。
- -XX:CompilerThreadStackSize 虚拟机内部编译线程大小。
- -XX:ParGCStridesPerThread=2 控制每次worker thread整几个Strides （改参数为诊断参数，须要-XX:+UnlockDiagnosticVMOptions参数来打开此隐藏参数）。
- -XX:-BindGCTaskThreadsToCPUs 开关参数，默认关闭。在操做系统容许的状况下绑定GC线程到单个CPU。开启该参数可减小thread的switching。
- -XX:-UseGCTaskAffinity 开关参数，默认关闭。开启该参数保证每次worker thread被唤醒的时候争取拿到上次没有完成的工做。（该选项在JDK7和JDK8版本好像不起做用。参考文章：[https://cloud.tencent.com/developer/article/1061456](http://www.javashuo.com/link?url=https://cloud.tencent.com/developer/article/1061456)）

注意：下面参数与StackOverflowError异常有关系。

- -XX:StackShadowPages=7 通常只有用到本地方法时才会去修改这个值，或则默认值已经可以知足需求，它具体指本地方法与JAVA共享栈，这个值是指在内存单元上，JAVA可以够调用本地方法的最大范围，而这个StackShadowPages 就是这个范围的最大值，通常地若是改变这个值，须要同时修改-Xss 或者-XX:ThreadStackSize= 修改线程的默认栈大小。而这个值会影响到JVM里面最大的线程数量，因此调整须要当心。通常状况下，在应用程序中，没有调用本地方法时，JVM的这个参数StackShadowPages没有必要修改。
- -XX:StackYellowPages=3 线程栈尾受保护内存区域yellow page。当方法的执行在线程栈中到达yellow page。则抛出StackOverflowError异常，注意此时jvm进程不会挂掉。
- -XX:StackRedPages=1 线程栈尾受保护内存区域red page。当方法的执行在线程栈中到达red page。进程会退出，并产生Crash日志。（若是程序的执行期间，没有栈空间了，而且程序的执行超过了red page地址。jvm会直接Crash掉，并不会产生Crash日志。此时因为栈空间不足，虚拟机没法进入信号处理程序，因此没法产生Crash日志。由此可知，控制-XX:StackYellowPages与-XX:StackRedPages两个参数，在必定程度上有助于帮助定位问题而且有助于让程序避免Crash，这个程度是有限的。）

## 锁调优

### 1. 偏向锁

- -XX:+UseBiasedLocking 开关参数。该参数开启关闭偏向锁。默认开启。并发状况比较严重的应用中，建议关闭此选项。
- -XX:BiasedLockingStartupDelay=0 正常偏向锁在应用启动几秒后才会激活。但可设置此参数为0，能够关闭偏向锁启动延迟。

### 2.轻量级锁

目前未找到优化参数

### 3.自旋锁

jdk1.7开始取消了自旋锁的优化参数。虚拟机自行调整。在此再也不详细说明。

### 4.锁消除

锁消除属于虚拟机在JIT编译时，对运行时上下文扫描运用逃逸技术，去除不可能存在共享资源竞争的锁的一种行为。因此想要开启锁消除必须同时使用以下两个参数。

- -XX:+DoEscapeAnalysis 开启逃逸分析 
- -XX:+EliminateLocks 开启锁消除

### 5.重量级锁

- -XX:-UseHeavyMonitors 开关参数。默认关闭。开启此参数能够关闭偏向锁和轻量级锁。

# JIT优化

> JIT官方参考：
> 
> [https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html)
> 
> [https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm)
> 
> [https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE](http://www.javashuo.com/link?url=https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE)

- -Xcomp，强制虚拟机运行于编译模式。
- -Xint，强制虚拟机运行于解释模式。
- -Xmixed，混合模式执行。这也是Oracle Hotspot VM默认的执行模式。
- -XX:CompileThreshold，数值参数。默认10000。该参数为方法调用计数器热点代码编译阈值。Client模式下默认1500次。Server模式下默认10000次。（注意：开启-XX:+TieredCompilation分层编译策略。虚拟机会忽略此参数设置）。
- -XX:InterpreterProfilePercentage，解释器监控比率，数值参数。默认33。用来计算server模式下回边计数器热点代码编译阈值。（公式：方法调用计数器阈值（CompileThreshold）× （OSR 比率（OnStackReplacePercentage）- 解释器监控比率（InterpreterProfilePercentage）） / 100）。
- -XX:OnStackReplacePercentage，数值参数。默认140。该参数用来调整回边计数器热点代码编译阈值。**（**client模式下，方法调用计数器阈值（CompileThreshold）× OSR 比率（OnStackReplacePercentage）/ 100，其中 OnStackReplacePercentage 默认值为 933，若是都取默认值，那 client 模式虚拟机的回边计数器的阈值为 13995。server模式下，方法调用计数器阈值（CompileThreshold）× （OSR 比率（OnStackReplacePercentage）- 解释器监控比率（InterpreterProfilePercentage）） / 100，其中 OnStackReplacePercentage 默认值为 140，InterpreterProfilePercentage 默认值为 33，若是都取默认值，那 server 模式虚拟机回边计数器的阈值为 10700。**）。**
- -XX:+AggressiveOpts，开关参数。默认关闭。启用积极的性能优化功能。开启此选项以后，须要考虑到性能的提高，一样也须要考虑到性能提高所带来的不稳定风险。须要彼此权衡。
- -XX:+UseCounterDecay，开关参数。默认开启。关闭后能够禁止JIT调用计数器热度衰减。默认状况下，每次GC时会对调用计数器进行砍半的操做，致使有些方法一直是个温热，可能永远都达不到C2编译的1万次的阀值。
- -XX:+TieredCompilation，开关参数。默认开启。多层编译策略，在某些状况下由于有些方法C1编译后C2再也不编译，多层编译反而比C2编译慢，若是发现此状况可进行禁止。（注意：开启此参数，虚拟机将会忽略-XX:CompileThreshold参数设置的值）

> 当启动分层编译时，虚拟机将再也不使用-XX:CompileThreshold指定的阈值(该参数值失效)，而是使用另外一套阈值系统，这套阈值系统是根据当前待编译的方法数目动态调整的。
> 
> -------------------------------------------------------------------------------------
> 
> 所谓动态调整并不复杂，在比较阈值时，Java虚拟机会将阈值与某个系数s相乘。该系数s与当前待编译的方法数目成正相关，与编译线程的数目成负相关。
> 
> 系数s的计算方法为：  
> s = queue_size_X / (TierXLoadFeedback * compiler_count_X) + 1
> 
> 其中 X 是执行层次，可取 3 或者 4；  
> queue_size_X 是执行层次为 X 的待编译方法的数目；  
> TierXLoadFeedback 是预设好的参数，其中 Tier3LoadFeedback 为 5，Tier4LoadFeedback 为 3；  
> compiler_count_X 是层次 X 的编译线程数目。
> 
> ---------------------------------------------------------------------------------------
> 
> 当启动分层编译时，即时编译具体触发条件以下：
> 
> 当方法调用次数大于由参数 -XX:TierXInvocationThreshold 指定的阈值乘以系数，或者当方法调用次数大于由参数 -XX:TierXMINInvocationThreshold 指定的阈值乘以系数，而且方法调用次数和循环回边次数之和大于由参数 -XX:TierXCompileThreshold 指定的阈值乘以系数时，便会触发 X 层即时编译。
> 
> 触发条件为：  
> i > TierXInvocationThreshold * s || (i > TierXMinInvocationThreshold * s  && i + b > TierXCompileThreshold * s)
> 
> 其中i为方法调用计数器次数，b为回边调用计数器次数。
> 
> ---------------------------------------------------------------------------------------
> 
> 当启动分层编译时，触发OSR编译的阈值则是由参数-XX:TierXBackEdgeThreshold指定的阈值乘以系数。
> 
> ---------------------------------------------------------------------------------------
> 
> 以下为JDK11.0.2 LTS版本针对JIT编译器分层编译策略的相关优化参数。因为资料太少。因此在此只黏贴相对默认值？
> 
>      intx Tier0BackedgeNotifyFreqLog               = 10                                        {product} {default}  
>      intx Tier0InvokeNotifyFreqLog                 = 7                                         {product} {default}  
>      intx Tier0ProfilingStartPercentage            = 200                                       {product} {default}  
>      intx Tier23InlineeNotifyFreqLog               = 20                                        {product} {default}  
>      intx Tier2BackEdgeThreshold                   = 0                                         {product} {default}  
>      intx Tier2BackedgeNotifyFreqLog               = 14                                        {product} {default}  
>      intx Tier2CompileThreshold                    = 0                                         {product} {default}  
>      intx Tier2InvokeNotifyFreqLog                 = 11                                        {product} {default}  
>      intx Tier3AOTBackEdgeThreshold                = 120000                                    {product} {default}  
>      intx Tier3AOTCompileThreshold                 = 15000                                     {product} {default}  
>      intx Tier3AOTInvocationThreshold              = 10000                                     {product} {default}  
>      intx Tier3AOTMinInvocationThreshold           = 1000                                      {product} {default}  
>      intx Tier3BackEdgeThreshold                   = 60000                                     {product} {default}  
>      intx Tier3BackedgeNotifyFreqLog               = 13                                        {product} {default}  
>      intx Tier3CompileThreshold                    = 2000                                      {product} {default}  
>      intx Tier3DelayOff                            = 2                                         {product} {default}  
>      intx Tier3DelayOn                             = 5                                         {product} {default}  
>      intx Tier3InvocationThreshold                 = 200                                       {product} {default}  
>      intx Tier3InvokeNotifyFreqLog                 = 10                                        {product} {default}  
>      intx Tier3LoadFeedback                        = 5                                         {product} {default}  
>      intx Tier3MinInvocationThreshold              = 100                                       {product} {default}  
>      intx Tier4BackEdgeThreshold                   = 40000                                     {product} {default}  
>      intx Tier4CompileThreshold                    = 15000                                     {product} {default}  
>      intx Tier4InvocationThreshold                 = 5000                                      {product} {default}  
>      intx Tier4LoadFeedback                        = 3                                         {product} {default}  
>      intx Tier4MinInvocationThreshold              = 600                                       {product} {default}  
>      bool TieredCompilation                        = true                                   {pd product} {default}  
>      intx TieredCompileTaskTimeout                 = 50                                        {product} {default}  
>      intx TieredRateUpdateMaxTime                  = 25                                        {product} {default}  
>      intx TieredRateUpdateMinTime                  = 1                                         {product} {default}  
>      intx TieredStopAtLevel                        = 4                                         {product} {default}

- -XX:TieredStopAtLevel=4，数值参数。默认值4。能够经过该参数限制多层编译策略多高能够用到哪一层。（共五层0-4）。
- -XX:+UseJVMCICompiler，开关参数。默认关闭。JDK9开始支持参数。使用将JIT编译器的C2替换为Graal。（这是一个实验性参数experimental，若是使用须要附加参数-XX:+UnlockExperimentalVMOptions）
- -XX:+DoEscapeAnalysis，开关参数。默认开启。表示开启逃逸分析，分析对象做用域不会逃逸出某个做用域。开启此参数后JIT编译器经过上下文扫描证实对象没法逃离方法体或当前线程，则会对对象进行进一步优化(栈上分配、同步消除/锁消除、标量替换)。
- -XX:CICompilerCount=3，数值参数。该参数表明总编译线程数量。client模式下默认为1，server模式下默认为2，若是开启分层编译策略-XX:+TieredCompilation那么该参数默认值将会根据CPU逻辑核心数量扩展。若是手动设置该参数CICompilerCountPerCPU将被设置为false。

> 对于四核及以上的机器，总的编译线程的数目为：  
> n = log2(N) * log2(log2(N)) * 3 / 2  
> 其中 N 为 CPU 核心数目。
> 
> 根据如上公式，四核CPU等于：2*1*3/2=3。也就是说四核CPU总编译线程数量为3。

- -XX:+CICompilerCountPerCPU，开关参数。默认开启。开启此参数编译线程数量CICompilerCount会根据CPU数量来调整。若是手动设置-XX:CICompilerCount参数的话。本参数将被设置为false。
- -XX:+BackgroundCompilation，开关参数。默认开启。也就是编译线程是后台运行的。若是关闭此参数，一旦达到 JIT 的编译条件，执行线程向虚拟机提交编译请求后将会一直等待，直到编译过程完成后再开始执行编译器输出的本地代码再继续执行方法或循环体。（也就是说若是关闭此参数，达到JIT编译条件的方法，不会与解释执行并发执行，必须等到JIT编译完成之后执行编译后的本地代码）。
- -XX:CodeCacheMinimumFreeSpace，数值参数。设置编译所需的最小可用空间（以字节为单位）。当剩余小于最小可用空间时，编译中止。（JDK8中可使用此参数。JDK11没有看到此参数。）
- -XX:CompileCommand=command,method[,option]，字符参数。该参数用于定制编译需求，好比过滤某个方法不作JIT编译,若未指定方法描述符，则对所有同名方法执行命令操做，可以使用星号通配符（*）指定类或方法。该参数可屡次指定，或使用 换行符（\n）分隔参数后的多个命令。

> 具体定制编译需求的命令：
> 
> exclude，跳过编译指定的方法  
> compileonly，只编译指定的方法  
> inline/dontinline，设置是否内联指定方法  
> print，打印生成的汇编代码  
> break，JVM以debug模式运行时，在方法编译开始处设置断点  
> quiet，不打印在此命令后续命令  
> log，排除-XX:+LogCompilation除指定方法以外的全部方法的编译日志记录（带选项）。默认状况下，对全部已编译的方法执行日志记录  
> option，此命令可用于将JIT编译选项传递给指定的方法以代替最后一个参数（选项）。编译选项在方法名称后面的末尾设置。您能够指定多个编译选项，以逗号或空格分隔。  
> 其余命令：help
> 
> 详细的定制编译需求的命令用法请参照官方文档：
> 
> [https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html),[https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE](http://www.javashuo.com/link?url=https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE)
> 
> 详细的option选项命令使用参数请参阅官方文档：
> 
> [https://docs.oracle.com/en/java/javase/11/vm/writing-directives.html#GUID-8ABE81D8-FABA-4871-86AA-998112994C44](http://www.javashuo.com/link?url=https://docs.oracle.com/en/java/javase/11/vm/writing-directives.html#GUID-8ABE81D8-FABA-4871-86AA-998112994C44)

- -XX:CompileCommandFile=filename，字符参数。设置从中读取JIT编译器命令的文件。默认状况下，该.hotspot_compiler文件用于存储JIT编译器执行的命令。命令文件中的每一行表明一个命令，一个类名和一个使用该命令的方法名。有关为JIT编译器指定要对方法执行的命令的更多信息，请参阅该-XX:CompileCommand选项。
- -XX:CompilerDirectivesFile=file，字符参数。该参数指定一个.json扩展名的指令文件添加到指令栈，以便于更精确的控制JIT的行为。（此参数为诊断参数，须要添加-XX:+UnlockDiagnosticVMOptions参数解锁此指令）。也能够经过jcmd指令对运行时jvm的指令栈进行控制而不须要重启jvm。

> jcmd pid Compiler.directives_add file   添加指令文件到pid指定jvm指令栈栈顶。  
> jcmd pid Compiler.directives_remove  移除pid指定jvm指令栈栈顶指令块，能够重复执行直到剩下默认指令块。但没法删除默认指令快。  
> jcmd pid Compiler.directives_clear      移除pid指定jvm指令栈中所有指令块，但没法删除默认指令块。  
> jcmd pid Compiler.directives_print       打印pid指定jvm指令栈。
> 
> 请参考官方文档：[https://docs.oracle.com/en/java/javase/11/vm/commands-work-directive-files.html#GUID-88ECC49E-4581-49F6-952F-0EB7E12E8C84](http://www.javashuo.com/link?url=https://docs.oracle.com/en/java/javase/11/vm/commands-work-directive-files.html#GUID-88ECC49E-4581-49F6-952F-0EB7E12E8C84)

- -XX:CompileOnly=methods，字符参数。-请谨慎使用-。该参数限制jvm编译的方法列表(以逗号分隔)。

> 例如：
> 
> -XX:CompileOnly=java/lang/String.length,java/util/List.size。
> 
> 该参数虽然不支持通配符，可是能够指定类或者包，进行所有编译。也能够指定方法名去编译全部类中方法。
> 
> 例如：
> 
> -XX:CompileOnly=java/lang/String  
> -XX:CompileOnly=java/lang  
> -XX:CompileOnly=.length

- -XX:+Inline，开关参数。默认开启。该参数指定是否启用方法内联。
- -XX:InlineSmallCode，数值参数。该参数指定只有已编译后的方法小于此字节值，才会被JIT内联优化。
- -XX:MaxInlineLevel，数值参数。该参数指定内联最大嵌套数。
- -XX:MaxInlineSize，数值参数。该参数指定若是非热点方法的字节码大小超过该值，则没法内联。
- -XX:MinInliningThreshold，数值参数。该参数指定若是目标方法的调用次数低于该值，则没法内联。
- -XX:+InlineSynchronizedMethods，开关参数。默认开启。该参数指定是否容许内联同步方法。
- -XX:FreqInlineSize，数值参数。该参数指定若是热点方法的字节码大小超过该值，则没法内联。
- -XX:LiveNodeCountInliningCutoff，数值参数。方法内联编译过程当中IR节点数目的上限。
- -XX:MaxNodeLimit，数值参数。该参数指定单个方法编译期间要使用的最大节点数。
- -XX:MaxTrivialSize，数值参数。该参数指定若是方法的字节码大小少于该值，则直接内联。
- -XX:+OptimizeStringConcat，开关参数。默认开启。该参数指定是否启动String串联操做优化。
- -XX:+UseAES，开关参数。默认开启。为Intel，AMD和SPARC硬件启用基于硬件的AES内在函数。Intel Westmere（2010及更新版本），AMD Bulldozer（2011及更新版本）以及SPARC（T4及更新版本）均为支持的硬件。UseAES与UseAESIntrinsics一块儿使用。
- -XX:+UseAESIntrinsics，开关参数。默认开启。默认状况下启用UseAES和UseAESIntrinsics标志，仅支持Java HotSpot Server VM 32位和64位。要禁用基于硬件的AES内在函数，请指定-XX:-UseAES -XX:-UseAESIntrinsics。可是若是选择使用AES指令，也同时须要两个参数同时使用。他们只支持jvm在server模式下运行。
- -XX:-UseCondCardMark，开关参数。默认关闭。在更新card table以前，能够检查卡是否已经标记。默认状况下禁用此选项，而且只应在具备多个套接字的计算机上使用此选项，从而提升严重依赖并发操做的Java应用程序的性能。在垃圾收集器marking阶段的一种优化方式，并与CMS收集器相关。但不影响G1。（因为官方把该参数放到了JIT高级优化参数里面，具体缘由还未查明，故此处也将此参数暂时放在这里）。
- -XX:UseCodeCacheFlushing:开关参数，默认开启。当JIT编译的本地代码缓存区接近或达到ReservedCodeCacheSize时，为了编译更多方法，JIT编译器必须抛弃一些已经编译的方法。若是关闭此参数。当codecache缓冲区满时，JIT编译器将中止工做。
- -XX:+UseSHA，开关参数。默认开启。为SPARC硬件启用SHA加密散列函数的基于硬件的内在函数。UseSHA与结合使用UseSHA1Intrinsics，UseSHA256Intrinsics和UseSHA512Intrinsics选项。
- -XX:+UseSHA1Intrinsics，开关参数。默认关闭。为SHA-1加密哈希函数启用内在函数。
- -XX:+UseSHA256Intrinsics，开关参数。默认开启。为SHA-224和SHA-256加密哈希函数启用内在函数。
- -XX:+UseSHA512Intrinsics，开关参数。默认开启。为SHA-384和SHA-512加密散列函数启用内在函数。
- -XX:+UseSuperWord，开关参数。默认开启。容许将标量操做转换为超级字操做。默认状况下启用此选项。
- -XX:-UseRTMDeopt，开关参数。默认关闭。根据停止率自动调谐RTM锁定。该比率由-XX:RTMAbortRatio选项指定。若是停止事务的数量超过停止率，则包含锁定的方法将被取消优化并从新编译，并将全部锁定为正常锁定。默认状况下禁用此选项。-XX:+UseRTMLocking必须启用该选项。
- -XX:-UseRTMLocking，开关参数。默认关闭。为全部膨胀的锁生成受限制的事务性内存（RTM）锁定代码，使用正常的锁定机制做为回退处理程序。默认状况下禁用此选项。与RTM相关的选项仅适用于支持事务同步扩展Transactional Synchronization Extension（TSX）的x86 CPU上的Java HotSpot Server VM。  
  RTM是英特尔TSX的一部分，它是x86指令集扩展，有助于建立多线程应用程序。RTM引入了新的指令XBEGIN，XABORT，XEND，和XTEST。该XBEGIN和XEND说明附上一组指令做为一个事务中运行。若是在运行事务时未发现冲突，则内存和寄存器修改将在XEND指令处一块儿提交。该XABORT指令可用于显式停止事务以及XEND检查是否在事务中运行一组指令的指令。  
  当另外一个线程尝试访问同一事务时，对事务的锁定会膨胀，从而阻止最初未请求访问该事务的线程。RTM要求在事务停止或失败时指定后备操做集。RTM锁是一种委托给TSX系统的锁。  
  RTM提升了在关键区域中具备低冲突的高竞争锁的性能（这是不能同时由多个线程访问的代码）。RTM还提升了粗粒度锁定的性能，这在多线程应用程序中一般表现不佳。（粗粒度锁定是长时间保持锁定以最小化获取和释放锁定的开销的策略，而细粒度锁定是经过仅在必要时锁定并尽快解锁来尝试实现最大并行性的策略。此外，对于不一样线程使用的轻度争用锁，RTM能够减小错误的缓存行共享，也称为缓存行乒乓。当来自不一样处理器的多个线程访问不一样的资源时会发生 但资源共享相同的缓存行。结果，处理器重复地使其余处理器的高速缓存行无效，这迫使它们从主存储器而不是它们的高速缓存读取

> 针对于RTM(Restricted Transactional Memory)介绍最好的一片文章：[JDK-8031320](http://www.javashuo.com/link?url=https://bugs.openjdk.java.net/browse/JDK-8031320)

- -XX:RTMAbortRatio，数值参数。RTM停止比率指定为全部已执行RTM事务的百分比（％）。若是许多停止事务变得大于此比率，则编译后的代码将被去优化。-XX:+UseRTMDeopt启用该选项时将使用此比率。此选项的默认值为50.这意味着若是50％的全部事务都被停止，则编译后的代码将被去优化。
- -XX:RTMLockingCalculationDelay，数值参数。该参数指定延迟终止比率计算时间，单位毫秒。
- -XX:RTMRetryCount，数值参数。RTM锁定代码将在停止或忙碌时重试此选项指定的次数，而后再回退到正常锁定机制。此选项的默认值为5。-XX:UseRTMLocking必须启用该选项。
- -XX:AllocateInstancePrefetchLines=1，数值参数。设置在实例分配指针以前预取的行数。默认状况下，预取的行数设置为1。
- -XX:AllocatePrefetchDistance，数值参数。设置对象分配的预取距离的大小（以字节为单位）。将从最后分配的对象的地址开始预取将要使用新对象的值写入的内存。每一个Java线程都有本身的分配点。负值表示基于平台选择预取距离。正值是预取的字节数。
- -XX:AllocatePrefetchInstr，数值参数。将预取指令设置为在分配指针以前预取。只有Java热点服务器VM支持此选项。可能的值从0到3。值后面的实际指令取决于平台。默认状况下为0。
- -XX:AllocatePrefetchLines，数值参数。使用编译代码中生成的预取指令设置在最后一次对象分配后要加载的高速缓存行数。若是最后分配的对象是实例，则默认值为1;若是是数组，则默认值为3。
- -XX:AllocatePrefetchStepSize，数值参数。设置顺序预取指令的步长。
- -XX:AllocatePrefetchStyle，数值参数。为预取指令设置生成代码样式。的风格参数是从0至3的整数。（0，不生成预取指令。1，每次分配后执行预取指令，默认值。2，使用TLAB水印指针来肯定什么时候执行预取指令。3，在SPARC上使用BIS指令进行分配预取。）

# 调试与问题排查

## jvm调试控制参数

- -XX:MaxJavaStackTraceDepth=1024 当发生StackOverflowError异常的时候，打印到日志里面的栈信息有必定可能性不是全的。必要的时候能够调整此值来保障栈信息的完整性。能够以将此值设置为-1，此时将会打印所有堆栈。
- -XX:-PrintCompilation，开关参数。默认关闭。开启此参数能够打印应用中JIT编译状况。打印内容：第一列是时间。第二列是Java虚拟机维护的编译ID。接下来是标识：%(是否OSR编译)，s(是否synchronized方法)，!(是否包含异常处理器)，b(是否租塞了应用线程，显示了b则表明编译线程不是后台运行，可了解一下参数-Xbatch)，n(是否为native方法)。再接下来则是编译层次，以及方法名。若是是OSR编译，那么方法名后面还会跟着@以及循环所在的字节码。
- -XX:-PrintGC，开关参数。默认关闭。为应用打印GC日志。
- -XX:-PrintGCDetails，开关参数。默认关闭。为应用打印GC详细日志。
- -Xloggc，字符参数，指定gc日志输出位置。例如：-Xloggc:/home/rock/gc_%t.log
- -Xlog，jdk11参数。该参数能够支持多种日志打印，例如gc日志（-Xlog:gc*:file=/home/rock/gc_%t_%p.log::filecount=5,filesize=20480），因为内容比较多。更详细的描述请参照官方文档[https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-BE93ABDC-999C-4CB5-A88B-1994AAAC74D5](http://www.javashuo.com/link?url=https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-BE93ABDC-999C-4CB5-A88B-1994AAAC74D5)
- -XX:-LogCompilation，开关参数。默认关闭。开启此参数会记录JIT编译活动到当前工做目录中名为hotspot.log的文件中，也可使用-XX:LogFile参数指定输出文件路径。（该参数为诊断参数，须要-XX:+UnlockDiagnosticVMOptions参数来打开此隐藏参数）。您也可使用-XX:+PrintCompilation参数，将冗长的JIT编译信息打印到控制台。
- -XX:-PrintInlining，开关参数。默认关闭。打印方法得到内联的信息到控制台。
- -XX:LogFile，字符参数。指定日志数据的写入文件的绝对路径。若是不指定，默认状况下，会在当前工做目录建立hotspot.log文件。
- -XX:-PrintAssembly，开关参数。默认关闭。打印装配字节码的native方法，须要外部连接库disassembler.so。
- -XX:-HeapDumpOnOutOfMemoryError，开关参数。默认关闭。启动该参数后发生OOM时自动导出heap信息。
- -XX:-HeapDumpAfterFullGC，开关参数。默认关闭。启动该参数后发生FullGC后自动导出heap信息。
- -XX:-HeapDumpBeforeFullGC，开关参数。默认关闭。启动该参数后发生FullGC前自动导出heap信息。
- -XX:HeapDumpPath，字符参数。该参数指定heapdump文件输出位置。
- -XX:-VerifyBeforeGC，开关参数。默认关闭。该参数是在GC前进行校验的参数，在校验过程当中当发生地址异常的化会打印出异常的地址，而且让JVM crash，由于这个参数每一次GC都要检查，包括新生代的GC，影响必定的性能，并不适合在产品环境中使用，但对发现GC中的对象问题，却很是有帮助。
- -XX:ErrorFile，字符参数。该参数指定jvm crash时候的错误信息输出位置。例如：-XX:ErrorFile=/path/hs_error%p.log，其中“%p”用来获取当前进程ID。
- -XX:OnError，字符参数。该参数指定jvm crash时候执行命令行。每一个命令以“;”分隔。
- -XX:-ShowMessageBoxOnError，开关参数。默认关闭。当jvm crash的时候在linux里会启动gdb 去分析和调式，适合在测试环境中使用。
- -XX:NativeMemoryTracking=[off | summary | detail]，字符选项参数。默认off。该参数用来开启NMT功能，用来排查堆外内存使用状况。虚拟机启动后可使用“jcmd <pid> VM.native_memory [summary | detail | baseline | summary.diff | detail.diff | shutdown] [scale= KB | MB | GB]”命令查看。(开启NMT会有5%-10%的性能耗损)
- -XX:-PrintNMTStatistics，开关参数。默认关闭。该参数指定在开启了NativeMemoryTracking参数后，在jvm退出时控制台打印NMT信息。
- -XX:G1SummarizeRSetStatsPeriod，数值参数。该参数指定了G1的RSet的统计周期，也就是通过多少次GC后进行一次统计。在JDK11下使用日志gc+remset=trace选项配合该参数，能够查看是否发生过RSet压缩coarsening。（该参数为诊断参数，须要-XX:+UnlockDiagnosticVMOptions参数来打开此隐藏参数）

## 几种OOM介绍

- **java.lang.OutOfMemoryError: Java heap space：**

       这种是java堆内存不够，一个缘由是真不够，另外一个缘由是程序中有对象建立没有销毁也就是错误的代码书写逻辑；   
　　若是是java堆内存不够的话，能够经过调整JVM下面的配置来解决：   
　　< jvm-arg>-Xms3062m < / jvm-arg>   
　　< jvm-arg>-Xmx3062m < / jvm-arg>   
　　 

- **java.lang.OutOfMemoryError: GC overhead limit exceeded** 

       【解释】：JDK6新增错误类型，当GC为释放很小空间占用大量时间时抛出；通常是由于堆过小，致使异常的缘由，没有足够的内存。   
　　【解决方案】：   
　　一、查看系统是否有使用大内存的代码或死循环；   
　　二、经过添加JVM配置，来限制使用内存：   
　　< jvm-arg>-XX:-UseGCOverheadLimit< /jvm-arg>   
　　 

- **java.lang.OutOfMemoryError: Metaspace：**

        这种是P区内存不够，可经过调整JVM的配置：   
　　< jvm-arg>-XX:MaxMetaspaceSize=128m< /jvm-arg>   
　　  

- **java.lang.OutOfMemoryError: Direct buffer memory：**

       调整-XX:MaxDirectMemorySize= 参数：   
　　< jvm-arg>-XX:MaxDirectMemorySize=128m< /jvm-arg>   
　　 

- **java.lang.OutOfMemoryError: unable to create new native thread** 

       【缘由】：Stack空间不足以建立额外的线程，要么是建立的线程过多，要么是Stack空间确实小了。   
　　【解决】：  
　　1.经过 -Xss启动参数减小单个线程栈大小，这样便能开更多线程（固然不能过小，过小会出现StackOverflowError）；   
　　2.经过-Xms -Xmx 两参数减小Heap大小，将内存让给Stack（前提是保证Heap空间够用）。   
　　 

- **java.lang.StackOverflowError** 

       【缘由】：这也内存溢出错误的一种，即线程栈的溢出，要么是方法调用层次过多（好比存在无限递归调用），要么是线程栈过小。   
　　【解决】：优化程序设计，减小方法调用层次；调整-Xss参数增长线程栈大小。

## jvm crash & heap dump & core dump & High CPU

### 模拟High CPU----分析

这个模拟很容易，简单的无限循环就能够作到。

使用top命令查找high cpu的进程：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/883_be4_ab5.jpg)

使用top -p 1997 -H命令查找high cpu的线程：

 ![](https://ewr1.vultrobjects.com/imgur2/000/005/187/884_52f_e1f.jpg)

转换2044为十六进制7fc。使用jstack -l 1977>jstack.1977.log命令导出thread dump。而后再文档中找到本地线程id为7fc的线程。能够很明显的看到当前线程执行的方法和正在作的high cpu的事情。

 ![](https://ewr1.vultrobjects.com/imgur2/000/005/187/885_982_0ef.jpg)

### core dump分析

- gdb分析：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/886_8ce_44e.jpg)

- jstack分析：（注意下面图片中的命令，在jdk11环境下须要借助jdk8的java命令才能够直接分析core文件。若是直接使用jdk11的java命令会提示：sun.jvm.hotspot.debugger.DebuggerException: cannot open binary file。）

![](http://static.javashuo.com/static/loading.gif)

- jmap分析：（注意下面图片中的命令，在jdk11环境下须要借助jdk8的java命令才能够直接分析core文件。若是直接使用jdk11的java命令会提示：sun.jvm.hotspot.debugger.DebuggerException: cannot open binary file。）

![](http://static.javashuo.com/static/loading.gif)

### 模拟jvm crash使用CrashAnalysis帮助分析crash 文件，简单例子：

想要模拟jvm crash并不容易，由于普通的java异常都已经被程序员、应用层、虚拟机拦截到了。因此想要模拟jvm crash必需要C/C++代码的配合。我们先看代码：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/889_429_db5.jpg)

helloworld.h文件：

/* DO NOT EDIT THIS FILE - it is machine generated */
#include <jni.h>
/* Header for class HelloWorld */

#ifndef _Included_com_test_rock_oom_TestCrash1
#define _Included_com_test_rock_oom_TestCrash1
#ifdef __cplusplus
extern "C" {
#endif
/*

* Class:     HelloWorld
* Method:    displayHelloWorld
* Signature: ()V
  */
  JNIEXPORT void JNICALL Java_com_test_rock_oom_TestCrash1_displayHelloWorld
  (JNIEnv *, jobject);

#ifdef __cplusplus
}
#endif
#endif

 helloworld.cpp文件：

#include "HelloWorld.h"
#include <stdio.h>
#include <jni.h>
/*

* Class:     HelloWorld

* Method:    displayHelloWorld

* Signature: ()V
  */
  JNIEXPORT void JNICALL Java_com_test_rock_oom_TestCrash1_displayHelloWorld
  (JNIEnv *, jobject) {
   int* p = NULL;
   *p = 3;
   //非法地址操做异常
  
   printf("Hello World!\n");
   return;
  }

 TestCrash1.java文件：

package com.test.rock.oom;

/**

* 测试jvm crash

* 启动时增长以下参数，指定crash日志打印位置

* -XX:ErrorFile="C:\workspaces\cresh_%p.log"
  */
  public class TestCrash1 {
   public native void displayHelloWorld();// java native方法申明
  
   static {
  
       System.load("C:/qt_workspace/build-helloworld-Desktop_Qt_5_12_2_MinGW_64_bit-Release/release/helloworld.dll");
  
   }
   public static void main(String[] args) {
  
       Thread t = new Thread(()->{
           TestCrash1 helloWorld = new TestCrash1();
           helloWorld.displayHelloWorld();
       });
       t.start();
  
   }
  }

**下面咱们来看CrashAnalysis的分析结论：**

这是首页，大概分析了问题的可能性：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/890_d70_960.jpg)

这个页面指定了出现问题的dll文件：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/891_d34_5f0.png)

这个页面指定了出现问题的线程以及堆栈信息：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/892_97c_0f7.jpg)

**经过分析crash的log文件咱们仍是能够进一步的查询到更详细的信息的：**

这里指定了有关crash的java代码部分能够很明显的定位：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/893_cd2_506.jpg)

crash问题总结：crash有很大一部分缘由是C/C++语言部分出现了问题致使的异常崩溃。换句话说在运行时角度上来说大部分缘由是机器码部分出现了问题致使了crash问题(咱们都知道jvm运行分为java字节码解释执行和JIT编译器编译后的机器码的直接执行)。因此说JIT编译器编译后的机器码也有可能会致使此类异常，若是因为JIT编译器致使了crash，那就须要详细排查目标java代码的编写所涉及到的上下文信息了。也可使用临时解决方案，JIT优化参数：-XX:CompileCommand的exclude指令，临时排除掉优化方法便可。

> 另附上linux下编译过程：
> 
> ![](http://static.javashuo.com/static/loading.gif)
> 
> g++ helloworld.cpp -c
> 
> ![](https://ewr1.vultrobjects.com/imgur2/000/005/187/895_44c_00b.png)
> 
> gcc -shared helloworld.o -o libHelloworld.so
> 
> ![](https://ewr1.vultrobjects.com/imgur2/000/005/187/896_ee5_4e9.png)

### 模拟OOM使用eclipse MAT分析heap dump文件，简单例子：

测试代码：



```
package com.test.rock.oom;
import java.util.ArrayList;

/**

- 测试oom
- 启动时增长以下参数使程序发生oom的时候能够打印堆栈信息到磁盘
- -XX:+HeapDumpOnOutOfMemoryError
  */

public class TestCrash {
 private TestCrash t1 = null;
 public static void main(String[] args){
 ArrayList<StubClass> list = new ArrayList<StubClass>();
 int count = 1000000000;
 for (int i = 0; i < count; i++) {
 StubClass bj = new StubClass();
 list.add(bj);
 }
 }
 static class StubClass {
 private Integer n = new Integer(123334);
 private Integer m = new Integer(123334);
 public void print() {
 System.out.println("m="+m);
 System.out.println("n="+n);
 }
 }
}
```



点击默认报告页面的See stacktrace。

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/897_b6d_b86.jpg)

从这个页面能够看出，出现了OOM异常。目标类是咱们的测试类TestCrash.class。

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/898_8c7_ff8.jpg)

在Overview页面点击Dominator Tree。

![](http://static.javashuo.com/static/loading.gif)

看到占用内存比例比较大的一项，继续点击向下查找。

![](http://static.javashuo.com/static/loading.gif)

这里能够看到，很明显是咱们的测试类中的内部类StubClass.class建立过多致使了OOM异常的发生。

![](http://static.javashuo.com/static/loading.gif)

## jdk命令行调试工具

### 1）jmap -histo:live <pid>

命令能够手动启动Full GC

### 2) jps [options]  [hostid]

jps主要用来输出JVM中运行的进程状态信息。

-q 输出类名、Jar名和传入main方法的参数  
-m 输出传入main方法的参数  
-l 输出main类或Jar的全限名  
-v 输出传入JVM的参数  
以下查看运行的java进程信息，打印jar名以及运行main方法传入的参数：  
![](https://ewr1.vultrobjects.com/imgur2/000/005/187/902_1a3_f2a.png)

### 3) jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]

jstat命令能够用于持续观察虚拟机内存中各个分区的使用率以及GC的统计数据。vmid是Java虚拟机ID，在Linux/Unix系统取进程ID。   
以下面输出的信息，采样时间间隔为1000ms，采样5次：

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/903_31b_63d.png)

上述各个列的含义：

- S0C、S1C、S0U、S1U：young代的Survivor 0/1区容量（Capacity）和使用量（Used）。0是FromSurvivor,1是ToSurvivor。
- EC、EU：Eden区容量和使用量
- OC、OU：年老代容量和使用量
- MC、MU：元数据区（Metaspace）已经committed的内存空间和使用量
- CCSC、CCSU：压缩Class（Compressed class space）committed的内存空间和使用量。
- YGC、YGT：young代GC次数和GC耗时
- FGC、FGCT：Full GC次数和Full GC耗时
- CGC 和 CGCT，它们分别表明并发 GC STW 的次数和时间
- GCT：GC总耗时

能够经过分区占用量上看到，在第2-3秒之间发生了一次YGC。YGC次数+1，而且Survivor from区的内存空间从1233.7->0,Survivor from从0->1536。Eden区也释放了不少内存空间。其余变化的空间占用也有元数据区以及元数据区的压缩Class区。Compressed class space也是元数据区的一部分，默认是1G,也能够关闭。具体的jvm8内存分布再也不详述。下一篇GC优化会再展开整理下。   
若是只看gc的总统计信息，也能够用jstat -gcutil vmid查询：  
![](http://static.javashuo.com/static/loading.gif)

### 4) jmap [option] pid

- jhsdb jmap --heap --pid <pid>或者jmap -heap <pid>查看heap状况

能够用来查看堆内存的使用详情。

![](http://static.javashuo.com/static/loading.gif)

- jmap -dump:format=b,file=jmap.hprof <pid>

生成内存快照文件，内存快照文件可使用eclipse MAT工具分析。

### 5）jstack [option] pid

jstack能够用来查看Java进程内的线程堆栈信息。

-l long listings，会打印出额外的锁信息，在发生死锁时能够用jstack -l pid来观察锁持有状况 

注意：当进程CPU使用率太高而致使没法打印线程堆栈信息时，可使用-F参数强行打印。

例如：jstack -F 23040或者jhsdb jstack --pid 23040

![](http://static.javashuo.com/static/loading.gif)

## 调试时的坑

- 执行jmap异常 operation not permitted

vim /etc/sysctl.d/10-ptrace.conf

kernel.yama.ptrace_scope = 0

- coredump

ulimit -c unlimited

## linux命令行调试工具

### top

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/907_441_ebc.jpg)

排查问题经常使用按键：

e：切换列表部分数值单位(k,m,g,t,p)

E：切换统计部分数值单位(k,m,g,t,p,e)

M：按照内存占用率排序列表

P：按照CPU占用率排序列表

经常使用列说明：

PID    进程ID  
USER   进程全部者用户名  
PR     优先级  
NI     nice值，负值表示高优先级  
VIRT   进程使用的虚拟内存总量，VIRT=SWAP+RES  
RES    进程使用的、未被换出的物理内存大小，RES=CODE+DATA  
SHR    共享内存大小  
S      进程状态，(D=不可中断的睡眠状态，R=运行，S=睡眠，T=跟踪/中止，Z=僵尸进程)  
%CPU   上次更新到如今的CPU时间占用百分比  
%MEM   进程使用的物理内存百分比  
TIME+  进程使用的CPU时间总计  
COMMAND命令名/命令行  
SWAP   进程使用的虚拟内存中，被换出的大小  
CODE   可执行代码占用的物理内存大小  
DATA   可执行代码之外的部分(数据段+栈)占用的物理内存大小

### pmap <pid>

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/908_66f_158.jpg)

第一列，内存块起始地址   
第二列，占用内存大小   
第三列，内存权限   
第四列，内存名称，anon表示动态分配的内存，stack表示栈内存   
最后一行，占用内存总大小，请注意，此处为虚拟内存大小，占用的物理内存大小能够经过top查看

## BTrace监控调试

一张图看懂BTrace工做原理

![](http://static.javashuo.com/static/loading.gif)

### 描述

        BTrace官网：[https://github.com/btraceio/btrace](http://www.javashuo.com/link?url=https://github.com/btraceio/btrace)

        BTrace是一个能够在不中止目标jvm进程，不修改java源代码的基础上对生产环境服务器上的jvm进程进行监控和测试。他使用ASM,instrument,JVMTI,Java Compiler Api等技术直接修改正在运行中java字节码信息，能够监控全部java方法的入参和出参。能够大大减小定位问题和问题排查的时间。虽然功能强大，但也有弊端，没法远程调试。可是对于排查问题，获取生产环境的关键数据、复现和定位问题来说。这些弊端已经算得上很是小了。由于若是问题不可以在开发环境或测试环境复现且必需要在生产环境获取必要数据，可是数据库以及程序员写的log又不能知足要求的时候，BTrace工具就显得格外的抢眼了(毕竟出了问题，就意味着下次重启jvm进程的时间已经很近了，并且如今哪一家公司的生产环境怎么会负载一台机器运行呢？)。对于生产环境大部分的问题，只有运行中的程序数据才能够表明一切，而在开发人员手中静止的代码是没有办法解决全部问题的。

        BTrace的功能强大请参见官网提供的样例代码：[https://github.com/btraceio/btrace/tree/master/samples](http://www.javashuo.com/link?url=https://github.com/btraceio/btrace/tree/master/samples)

### 支持jdk11

        BTrace官方的版本只支持到jdk8。本人亲测在jdk1.8.0_201版本下能够运行官方版本。为了可以在后续版本中使用。小编修改了BTrace源代码和ASM源代码，才保证在jdk11下通畅运行。主要问题在于jdk9之后出现的模块化技术与愈来愈严格的jdk内部类的使用权限、还有在于官方的btrace-asm-5.0.4.jar是基于asm-all-5.2.jar，因此asm版本有点低，支持的jdk版本过低了。固然了也没这么简单，小编在这里再也不详述了。小编在这里提供了支持jdk11版本的资源供你们下载，linux系统覆盖btrace、btracec文件和三个jar，win系统覆盖btrace.bat、btracec.bat文件和三个jar便可使用。小编亲测在win10 x64专业版、Ubuntu18.04.2LTS两个系统，jdk1.8.0_201和jdk11.0.2 LTS两个版本。均已跑通全部官方提供的BtraceScript样例代码(排除DTrace相关)。

资源：[支持Oracle jdk11版本BTrace](http://www.javashuo.com/link?url=https://download.csdn.net/download/jl19861101/11094805)

### jdk11小测试

测试java代码

```

```



windows测试--目标进程

![](http://static.javashuo.com/static/loading.gif)

windows测试--监控进程

![](http://static.javashuo.com/static/loading.gif)

linux测试--目标进程

![](http://static.javashuo.com/static/loading.gif)

linux测试--监控进程

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/913_20c_4e7.png)

### webBTraceUtil小工具

一张图看懂小工具工做原理

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/914_357_7b2.jpg)

 小工具页面

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/915_c67_364.jpg)

使用须知： 

- connect按钮用来启动socket连接
- Clean Up和Clean Up All两个按钮用来中止并清除BTrace测试脚本的任务。这点很重要。这就比如使用BTrace官方版命令行Ctrl+C，而后选择1。是同样的效果。目的是用来把已经在目标jvm中运行Class字节码再次修改回来。咱们都知道BTrace是经过各类技术修改正在运行中java字节码信息达到目的的。咱们若是没有在退出的时候修改回来，这个被修改的Class信息会永远停留在目标jvm中。即使如此官方BTrace也添加了有效的ShutdownHook与Signal(SIGINT)。因此网上其余文章或教程等所说的BTrace修改的Class字节码信息会永远停留在目标jvm进程中的这个观点是错误的。小编在这里为官方BTrace“平反”呵呵。咱们再说回这两个按钮，Clean Up的做用是中止并清除当前页面发起的BTrace测试脚本任务，Clean Up All的做用是中止并清除全部页面发起的BTrace测试脚本任务。(若是你们不当心关闭页面，或者刷新页面的话。不用担忧。小工具会自动中止并清除当前页面发起的BTrace测试脚本任务。或者你们能够不用点击这两个按钮，直接关闭页面便可，也能够达到Clean Up的效果。)

> **对于小编为官方BTrace“平反”的证据以下**：主要涉及到BTrace官方源代码com.sun.btrace.agent.Client类中：retransformLoaded()与OnExit()两个方法。另一个重要的类com.sun.btrace.runtime.BTraceTransformer类的transform()方法。在一个BTrace脚本的生命周期中，会执行两次retransformLoaded()方法，第一次的目的是为了修改目标Class的字节码是为了测试数据。第二次执行目的是为了修正回原来类的字节码。
> 
> ![](http://static.javashuo.com/static/loading.gif)
> 
> ![](https://ewr1.vultrobjects.com/imgur2/000/005/187/917_5a1_f76.jpg)
> 
> Client.OnExit()方法中cleanupTransformers()方法的执行清除了目标类过滤器。BTraceTransformer.transform()方法，会在if(filter.matchClass******这一行返回NULL。
> 
> 引用Oracle官方：If the implementing method determines that no transformations are needed, it should return `null`. Otherwise, it should create a new `byte[]` array, copy the input `classfileBuffer` into it, along with all desired transformations, and return the new array. The input `classfileBuffer` must not be modified.
> 
> 详见：[https://docs.oracle.com/javase/8/docs/api/java/lang/instrument/ClassFileTransformer.html](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/docs/api/java/lang/instrument/ClassFileTransformer.html)
> 
> 小编在这里再也不多说了。请参考BTrace官方源代码，或Oracle官方对于ClassFileTransformer.transform()方法的描述。再或者开启BTrace的debug模式查看详细的日志输出。再~再~再~在不行，请自行编写instrumentation相关技术代码本身测试或者参见小编写的测试代码：[https://pan.baidu.com/s/1vRT8lGSoi1bsDNWNuo-UCQ](http://www.javashuo.com/link?url=https://pan.baidu.com/s/1vRT8lGSoi1bsDNWNuo-UCQ) 提取码 r7n2。

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/918_c8b_642.png)

- 小编在这里再剔除网上的另外一个谣言。BTrace官方版本原本就支持远程调试，对于这一点不须要修改任何源代码。(即使如此，小编在此也不建议你们在生产环境下进行远程调试)

> java -javaagent:"C:\电脑搬家资料拷贝-重要\类库\btrace\btrace1\build\btrace-agent.jar"=port=2020,statsd=,debug=true,bootClassPath=.,probeDescPath=. --add exports=java.base/jdk.internal.misc=ALL-UNNAMED TestBt
> 
> ![](http://static.javashuo.com/static/loading.gif)
> 
> 这里请注意：虽然远程调试的时候PID没有任何意义。可是若是没有会报错。 
> 
>  ![](http://static.javashuo.com/static/loading.gif)
> 
>  ![](http://static.javashuo.com/static/loading.gif)

- debug开关按钮，功能等同于命令行-v的选项开关。小编自认为debug功能比较有用因此显示的添加了一个按钮。开启此功能，debug信息会打印到目标JVM进程控制台与webBTraceUtil小工具控制台。 
- 当BTrace脚本中有OnEvent注释的时候，可使用event按钮发送指定事件到agent端。窗口中输入字符，等同于官方命令行Ctrl+C选择3的状况。窗口中不输入字符，等同于官方命令行Ctrl+C选择2的状况。 
- BTrace脚本请输入在最上面的textarea中。
- BTrace命令行命令，请输入在BTrace command line输入框中。

> 1. 输入规则是剔除原命令行中btrace字符和脚本文件的输入便可，其余规则和命令行的同样。  
>    例如：  
>    命令行：btrace 8952 BtraceScript.java     小工具：8952  
>    命令行：btrace --version                           小工具：--version  
>    命令行：btrace -v 8952 BtraceScript.java 小工具：-v 8952
> 
> 2. 小工具剔除了-I选项。此选项在小工具中不起做用。
> 
> 3. webBTraceUtil的BTrace command line命令行输入框中，没有官方原命令行中的引号那么一说，能够直接省略。这点请注意。
> 
> 例如：这里的classpath路径不须要引号
> 
> ![](http://static.javashuo.com/static/loading.gif)

- GO按钮的做用是发送BTrace脚本以及BTrace命令到小工具后台并启动测试任务。若是重复点击GO按钮，小工具会自动中止并清除掉上一个本页面发起的测试脚本任务，并执行新的测试任务。
- 当前页面发起的测试脚本任务返回信息会显示在最早面的textarea中。
- 本小工具支持多人同时测试,互不影响。但也要注意，请给生产环境留下喘气的时间。
- 小工具不支持Dtrace测试。请使用官方版本或小编提供的支持jdk11版本。
- 使用webBTraceUtil小工具，不须要安装BTrace官方版本，也不须要配置任何BTRACEHOME环境变量。除了如下注意事项之外直接拷贝直接使用。

> 注意：  
> jdk1.8版本请拷贝JAVA_HOME/lib目录下的tools.jar。到webBTraceUtil/lib目录下。注意这里必定是测试目标机器的tools.jar。win版本jdk的tools.jar没法在linux版本jdk下使用。  
> jdk11版本，不须要拷贝tools.jar由于没有。可是所测试的目标JVM进程，启动参数必须添加--add-exports java.base/jdk.internal.misc=ALL-UNNAMED不然没法运行。

小编提供了两个版本web小工具，一个支持jdk1.8一个支持jdk11。

资源：[webBTraceUtil_for_JDK11](http://www.javashuo.com/link?url=https://download.csdn.net/download/jl19861101/11158504)      [webBTraceUtil_for_JDK1.8](http://www.javashuo.com/link?url=https://download.csdn.net/download/jl19861101/11158505)

### BTrace总结：

- 本文全部内容，以及小编提供的支持jdk11版本的BTrace，和两个BTrace的web小工具。都是基于官方BTrace1.3.11.3版本的源代码。
- 想要正确从目标jvm进程获取汉字不乱码。就必须在目标JVM所运行的class字节码编译的时候使用utf-8格式编译。而BTrace脚本文件也必须使用utf-8编码格式。javac -encoding utf-8 TestBt.java
- 在JDK11下运行程序，目标jvm进程启动时须要增长--add-exports java.base/jdk.internal.misc=ALL-UNNAMED参数。由于agent.jar使用了一些jdk.internal.misc包下的类。
- 因为BTrace的agaent端和client端使用Socket技术通讯。因此建议不要进行远程监控调试。(建议直接使用官方的本地BTrace脚本调试方式或者小编提供的webBTraceUtil小工具)
- BTrace默认会占用服务器2020端口。请注意服务器端口或修改。
- 请使用对应版本，在jdk1.8下编译出来的BTrace没法在jdk11运行并attach到jdk11的JVM进程。
- 有任何问题，请加小编微信：longyunjiangliang。小编也但愿可以与您有更多的技术交流。

## 可视化的GC日志分析

可很直观的查看到GC调优重要性能指标----吞吐量、STW时间。

### GCEasy

[https://gceasy.io/](http://www.javashuo.com/link?url=https://gceasy.io/)

### GCViewer

![](https://ewr1.vultrobjects.com/imgur2/000/005/187/923_ce3_1fc.jpg)

 ![](https://ewr1.vultrobjects.com/imgur2/000/005/187/924_673_a0c.jpg)

## 图形界面性能监控工具

### 开启远程监控-JMX

-Dcom.sun.management.jmxremote  
-Dcom.sun.management.jmxremote.port=9999  
-Dcom.sun.management.jmxremote.authenticate=false  
-Dcom.sun.management.jmxremote.ssl=false

### 开启远程监控-jstatd

- JDK11

grant codebase "jrt:/jdk.jstatd" {    
   permission java.security.AllPermission;    
};

grant codebase "jrt:/jdk.internal.jvmstat" {    
   permission java.security.AllPermission;    
};

- JDK8 

grant codebase "file:${java.home}/../lib/tools.jar" {
    permission java.security.AllPermission;
};

启动RMI服务：jstatd -J-Djava.rmi.server.hostname=192.168.1.8 -J-Djava.security.policy=./jstatd.jdk11.policy -p 1100

### VisualVM

- File->Load->Thread Dump(对应的是jstack生成的线程栈文件)
- File->Load->Heap Dump(对应的是jmap生成的堆栈文件)

### JDK Mission Control 7.1.0

最新JMS7.1.0编译并运行：

> **第一：下载安装版本控制mercurial**
> 
> Mac OS X：brew install mercurial
> 
> Windows  ： [https://www.mercurial-scm.org/wiki/Download](http://www.javashuo.com/link?url=https://www.mercurial-scm.org/wiki/Download)
> 
> linux         ：sudo apt-get install mercurial
> 
> **第二：下载JMC源代码**
> 
> hg clone [http://hg.openjdk.java.net/jmc/jmc/](http://www.javashuo.com/link?url=http://hg.openjdk.java.net/jmc/jmc/)
> 
> **第三：安装Maven**
> 
> [https://maven.apache.org/install.html](http://www.javashuo.com/link?url=https://maven.apache.org/install.html)
> 
> **第四：编译(此处须要jdk8或后续版本，小编使用jdk11.0.2编译经过)。此过程首次分为三个小步骤，后续编译只须要最后一步** 
> 
> 打开一个命令行窗口：
> 
> - cd releng/third-party  
>   mvn p2:site  
>   mvn jetty:run
> 
> 开启一个新的命令行窗口：
> 
> - cd core  
>   mvn install
> - mvn clean package
> 
> **第五：运行**
> 
> Windows:target\products\org.openjdk.jmc\win32\win32\x86_64\jmc.exe -vm %JAVA_HOME%\bin
> 
> Mac OS X:target/products/org.openjdk.jmc/macosx/cocoa/x86_64/JDK\ Mission\ Control.app/Contents/MacOS/jmc -vm $JAVA_HOME/bin

> JDK 11：
> 
> -XX:StartFlightRecording
> 
> JDK 9，JDK 10：
> 
> -XX:+UnlockCommercialFeatures
> -XX:StartFlightRecording
> 
> JDK 8u40，
> 
> -XX:+UnlockCommercialFeatures
> -XX:StartFlightRecording=name=whatever
> 
> JDK 7u4，JDK 8u20：
> 
> -XX:+UnlockCommercialFeatures 
> -XX:+FlightRecorder 
> -XX:StartFlightRecording=name=whatever
> 
> 若是要避免所有附加到进程，能够指定一个文件名，即-XX：StartFlightRecording=filename=rec.jfr，而后经过设置持续时间(即工期=60)转储记录，或者在JVM退出：
> 
> -XX:StartFlightRecording=
>  filename=rec.jfr,dumponexit=true
> 
> 能够在JMC中打开而后打开记录文件，在JDK 11中，这已进一步简化，所以只需指定：
> 
> -XX:StartFlightRecording:filename=c:\recs
> 
> 而且文件名将在指定的目录中生成，并在JVM退出时自动转储
> 
> 内容来自：[https://cloud.tencent.com/developer/ask/151108/answer/262468](http://www.javashuo.com/link?url=https://cloud.tencent.com/developer/ask/151108/answer/262468)

### JProfile

[https://download.csdn.net/download/jl19861101/11078717](http://www.javashuo.com/link?url=https://download.csdn.net/download/jl19861101/11078717)

# 总结

## JVM性能调优思路

(本文只从理论而不是实际效果的角度，客观的评论方式方法的存在)

1.内存调优  
2.GC调优  
3.多线程调优  
4.JIT调优

## JVM生产环境问题排查思路

(本文只从JVM层面阐述关注点，不包括程序员在代码层面以及开发阶段的问题排查方式方法)

![](http://static.javashuo.com/static/loading.gif)

> 参考资料：
> 
> [https://docs.oracle.com/en/java/javase/11/vm/compiler-control1.html#GUID-94AD8194-786A-4F19-BFFF-278F8E237F3A](http://www.javashuo.com/link?url=https://docs.oracle.com/en/java/javase/11/vm/compiler-control1.html#GUID-94AD8194-786A-4F19-BFFF-278F8E237F3A)
> 
> [https://engineering.linkedin.com/garbage-collection/garbage-collection-optimization-high-throughput-and-low-latency-java-applications](http://www.javashuo.com/link?url=https://engineering.linkedin.com/garbage-collection/garbage-collection-optimization-high-throughput-and-low-latency-java-applications)
> 
> [https://docs.oracle.com/javase/9/gctuning/garbage-first-garbage-collector-tuning.htm#JSGCT-GUID-4914A8D4-DE41-4250-B68E-816B58D4E278](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/9/gctuning/garbage-first-garbage-collector-tuning.htm#JSGCT-GUID-4914A8D4-DE41-4250-B68E-816B58D4E278)
> 
> [https://docs.oracle.com/javase/10/gctuning/garbage-first-garbage-collector-tuning.htm#JSGCT-GUID-2428DA90-B93D-48E6-B336-A849ADF1C552](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/10/gctuning/garbage-first-garbage-collector-tuning.htm#JSGCT-GUID-2428DA90-B93D-48E6-B336-A849ADF1C552)
> 
> [https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-9E847B7E-1F6B-4AD4-A5EE-66F8EF8BA9F6](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-9E847B7E-1F6B-4AD4-A5EE-66F8EF8BA9F6)
> 
> [https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html)
> 
> [https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc_tuning.html)
> 
> [https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm)
> 
> [https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html)
> 
> [https://www.oracle.com/technetwork/cn/articles/java/g1gc-1984535-zhs.html](http://www.javashuo.com/link?url=https://www.oracle.com/technetwork/cn/articles/java/g1gc-1984535-zhs.html)
> 
> [http://openjdk.java.net/jeps/197](http://www.javashuo.com/link?url=http://openjdk.java.net/jeps/197)
> 
> [java虚拟机高级参数及java命令大全 基于java8及以上 - JavaShuo](http://www.javashuo.com/article/p-fouztpof-a.html)
> 
> [https://blog.csdn.net/jl19861101/article/details/88566316](http://www.javashuo.com/link?url=https://blog.csdn.net/jl19861101/article/details/88566316)
> 
> [https://blog.csdn.net/foolishandstupid/article/details/77596050](http://www.javashuo.com/link?url=https://blog.csdn.net/foolishandstupid/article/details/77596050)
> 
> [https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html)
> 
> [https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE](http://www.javashuo.com/link?url=https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE)
> 
> [https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html#large_pages](http://www.javashuo.com/link?url=https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html#large_pages)
> 
> 《深刻理解Java虚拟机_JVM高级特性与最佳实践 第2版》
> 
> 《实战Java虚拟机：JVM故障诊断与性能优化》
> 
> 《Java性能优化权威指南》
