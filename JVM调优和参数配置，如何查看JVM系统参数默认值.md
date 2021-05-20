JVM调优和参数配置，如何查看JVM系统参数默认值

一、JVM的参数类型：

1、标配参数：

    java -version 
    java -help

2、X参数：

-Xmixed 混合模式（先编译后执行）

-Xint  解释执行

-Xcomp 第一次使用就编译成本地代码


    C:\Users>java -version
    java version "10.0.1" 2018-04-17
    Java(TM) SE Runtime Environment 18.3 (build 10.0.1+10)
    Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10.0.1+10, mixed mode)
    
    C:\Users>java -Xint -version
    java version "10.0.1" 2018-04-17
    Java(TM) SE Runtime Environment 18.3 (build 10.0.1+10)
    Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10.0.1+10, interpreted mode)
    
    C:\Users>java -Xcomp -version
    java version "10.0.1" 2018-04-17
    Java(TM) SE Runtime Environment 18.3 (build 10.0.1+10)
    Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10.0.1+10, compiled mode)

3、XX参数：

Boolean类型：公式：-XX:+ (+表示开启 -表示关闭)

     问题：如何查看一个正在运行中的java程序，它的某个JVM参数是否开启？

```
public class HelloGC {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("*****helloGC");
        Thread.sleep(Integer.MAX_VALUE);
    }
}

jps -l 
jinfo -flag PrintGCDetails 10729
```

结果：-XX:+PrintGCDetails，表示开启打印 GC 收集细节（默认不开启）　

KV设值类型：公式：-XX:属性key=属性值value=

    jps -l
    jinfo -flag MetaspaceSize 6932  --若没有配置参数，则查看系统参数默认值

结果：-XX:MetaspaceSize=21807104

    jinfo -flag 配置项 进程编号
    jinfo -flags 进程编号


二、如何查看JVM系统参数的默认值

    java -XX:+PrintFlagsInitial  --查看出厂默认值
    java -XX:+PrintFlagsFinal  --查看修改更新  (= 没有修改过  := 人为修改过)
    java -XX:+PrintFlagsFinal -XX:MetaspaceSize=512m   查看系统参数，并修改元空间大小 
 

    java -XX:+PrintCommandLineFlags -version  --打印命令行参数(可以看默认垃圾回收器)　　

结果：-XX:InitialHeapSize=134217728 -XX:MaxHeapSize=2147483648 -XX:+PrintCommandLineFlags
-XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseParallelGC

    java version "1.8.0_111"
    Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
    Java HotSpot(TM) 64-Bit Server VM (build 25.111-b14, mixed mode)　

 

三、两个经典参数：-Xms1024m -Xmx1024m谈谈你的理解

    -Xms等价于 -XX:initialHeapSize
    -Xmx等价于 -XX:MaxHeapSize