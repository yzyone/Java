
### Java 生成 JNI 头文件 ###


【参考文章】：[javah javac 命令行生成native的方法教程](https://www.2cto.com/database/201806/756601.html)

【参考文章】：[用javah 导出类的头文件， 常见的错误及正确的使用方法](https://blog.csdn.net/hejinjing_tom_com/article/details/8125648)

　　项目需要调用 C++动态库，由于项目包结构发生变化，需要重新生成本地方法的头文件，记录一下基本使用方法。

使用 javac 生成 JNI 头文件

基本使用方法

JDK版本必须在 1.8 以上 执行命令

    javac  -encoding  utf8  -h targetDir   sourceFile

示例

Java文件目录结构

1.2.2  在 NativeCpp.java 文件所在目录打开命令行

1.2.3  执行命令 

    javac -encoding utf8 -h . NativeCpp.java

（注意 点 后面有个空格）
　　

    -encoding  utf8   指定源文件编码格式
    
    -h  .  头文件的输出目录，. 表示当前目录，后面必需加个空格
    
    NativeCpp.java  源文件

1.2.4 结果
　　

3、生成的头文件内容

```
/* DO NOT EDIT THIS FILE - it is machine generated */
#include <jni.h>
/* Header for class com_streamax_s17_fms_business_jni_NativeCpp */

#ifndef _Included_com_streamax_s17_fms_business_jni_NativeCpp
#define _Included_com_streamax_s17_fms_business_jni_NativeCpp
#ifdef __cplusplus
extern "C" {
#endif
/*
 * Class:     com_streamax_s17_fms_business_jni_NativeCpp
 * Method:    parseBlacBox
 * Signature: (Ljava/lang/String;II)Ljava/lang/String;
 */
JNIEXPORT jstring JNICALL Java_com_streamax_s17_fms_business_jni_NativeCpp_parseBlacBox
  (JNIEnv *, jobject, jstring, jint, jint);

#ifdef __cplusplus
}
#endif
#endif
```

使用 javah 生成 JNI 头文件

基本使用方法

　　在包结构开始的目录打开命令行窗口；

　　执行命令：

    javah -d targetDir  -classpath . 包路径+类名（类名不包含文件后缀名）

在包结构开始的目录打开命令行窗口

　　在源代码包结构开始处或者class文件包结构开始处都可以；

执行命令：

    javah -classpath . com.streamax.s17.fms.business.jni.NativeCpp
    
    -classpath  .  指定加载类的路径，因为当前目录是包结构开始的目录，所以使用 . ，表示当前目录；
    
    com.streamax.s17.fms.business.jni.NativeCpp  本地方法所在的包路径+类名（注意没有.java或者.class的后缀）；

结果　　

　　不指定输出路径，生成的头文件默认在当前目录下；

　　使用 -d  targetDir 指定输出路径，

    javah -d ../ -classpath . com.streamax.s17.fms.business.jni.NativeCpp

此处指定头文件生成在当前目录的父目录

2.1.4 头文件内容

```
/* DO NOT EDIT THIS FILE - it is machine generated */
#include <jni.h>
/* Header for class com_streamax_s17_fms_business_jni_NativeCpp */

#ifndef _Included_com_streamax_s17_fms_business_jni_NativeCpp
#define _Included_com_streamax_s17_fms_business_jni_NativeCpp
#ifdef __cplusplus
extern "C" {
#endif
/*
 * Class:     com_streamax_s17_fms_business_jni_NativeCpp
 * Method:    parseBlacBox
 * Signature: (Ljava/lang/String;II)Ljava/lang/String;
 */
JNIEXPORT jstring JNICALL Java_com_streamax_s17_fms_business_jni_NativeCpp_parseBlacBox
  (JNIEnv *, jobject, jstring, jint, jint);

#ifdef __cplusplus
}
#endif
#endif
```
 
如果文章对您有所帮助，可以点一下推荐哦

