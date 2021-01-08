# JNI实战（三）：JNI 数据类型映射 #

在JNI实战（二）：Java 调用 C 我们了解了JNI的静态注册和动态注册。也知道我们应该使用动态注册来进行JNI函数与Java方法之间的映射。

示例的映射表的数组为如下：

```
static JNINativeMethod g_methods[] = {
        // Java层方法、参数类型、JNI方法
        {"_jnitest", "()Ljava/lang/String;", (void*)my_test_register}
};
```

我们第一个参数为Java层的方法，第三个参数为C层的方法，第二个参数为参数类型。这里()Ljava/lang/String;表明JNI函数的输入为空，输出为Java的String对象。

但是，如果我们想传递其他类型参数或者对象呢？ 下面我们来介绍一下JNI的数据类型映射：

1. 基本数据类型
基本的数据类型映射如下：

|Java类型|	Native类型|	域描述符|
|boolean|	jboolean 	Z|
|byte| 	jbyte|	B|
|char|	jchar|	C|
|short|	jshort|	S|
|int|	jnit|	I|
|long|	jlong|	J|
|float|	jfloat|	F|
|double|	jdouble|	D|
|void|	void|	V|

 2. 数组引用类型
如果是一维数组则遵循下表，如果是二维数组或更高维数组则对应的 Natvie类型为 jobjectArray，域描述符中使用 ‘[’ 的个数表示维数：

|Java类型|	Native类型|	域描述符|
|int[]|	jintArray|	[I|
|float[]|	jfloatArray|	[f|
|byte[]|	jbyteArray|	[B|
|char[]|	jcharArray|	[C|
|short[]|	jshortArray|	[S|
|double[]|	jdoubleArray|	[D|
|long[]|	jlongArray|	[F|
|boolean[]|	jbooleanArray|	[Z|

 3. 对象引用类型
对于其它引用类型，即 java 中的对象，其映射规则为：

|Java类型|	Native类型|	域描述符|
|类名（如 Surface）	
|通常是 jobject，仅有一种例外，如果 java 类型是 String，则对应的native 类型是 jstring
|以”L”开头，以”;”结尾中间是用”/” 隔开的包及类名（如 Landroid/view/Surface;）如果内部类则使用$连接内部类；|

4. 对象数组引用类型
如果是一维数组则遵循下表，如果是二维数组或更高维数组则对应的 native 类型为 jobjectArray，域描述符中使用 ‘[’ 的个数表示维数：

|Java类型|	Native类型|	域描述符|
|类名（如 Surface）	
|通常是 jobject，仅有一种例外，如果 java 类型是 String，则对应的native 类型是 jstring
|在对象引用类型的域描述符的基础上在左边添加’[‘字符|

分类: JNI 实战

原文链接：https://www.cnblogs.com/renhui/p/10492430.html