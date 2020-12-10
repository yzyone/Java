# What is the StackShadowPages #


A guy from the sun said as the following:

"

I did find a few snippits here and there, These are from Troubleshooting Guide for Java SE 6 with HotSpotVM

In theHotSpot implementation, Java methods share stack frames with C/C++ native code,
namely user native code and the virtual machine itself. Java methods generate code that checks
that stack space is available a fixed distance towards the end of the stack so that the native code
can be called without exceeding the stack space. This distance towards the end of the stack is
called “Shadow Pages.” The size of the shadow pages is between 3 and 20 pages, depending on
the platform. This distance is tunable, so that applications with native code needing more than
the default distance can increase the shadow page size. The option to increase shadow pages is
-XX:StackShadowPages=n, where n is greater than the default stack shadow pages for the
platform.

If you increase the value of StackShadowPages, you might also need to increase the default
thread stack size using the -Xssparameter. Increasing the default thread stack size might
decrease the number of threads that can be created, so be careful in choosing a value for the
thread stack size. The thread stack size varies by platform from 256k to 1024k."

 

StackShadowPages 一般只有用到本地方法时才会去修改这个值，或则默认值已经能够满足需求，它具体指本地方法与JAVA共享栈，这个值是指在内存单元上，JAVA能够够调用本地方法的最大范围，而这个StackShadowPages 就是这个范围的最大值，一般地如果改变这个值，需要同时修改-Xss 或者-XX:ThreadStackSize= 修改线程的默认栈大小。而这个值会影响到JVM里面最

大的线程数量，所以调整需要小心。

 

一般情况下，在应用程序中，没有调用本地方法时，JVM的这个参数StackShadowPages没有必要修改。 

非Stable参数（-XX），此类参数各个jvm实现会有所不同，将来可能会随时取消，需要慎重使用（但是，这些参数往往是非常有用的）. 通过命令`java -XX:+PrintFlagsFinal`，可以查看StackShadowPages数值。


http://forums.oracle.com/forums/thread.jspa?messageID=9767929&tstart=0