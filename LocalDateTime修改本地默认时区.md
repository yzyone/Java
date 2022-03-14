# LocalDateTime修改本地默认时区

[凡人歌者徐](https://www.zhihu.com/people/you-ke-26-20-52)

爱健身的程序员

LocalDateTime时间API底层使用的默认时区（作者当前坐标上海）

```java
public class DateTimeTest {

    public static void main(String[] args) {
        System.out.println("当前本地时区："+ ZoneId.systemDefault());
        System.out.println("当前日期和时间：" + LocalDateTime.now());
    }
}

// 控制台打印
当前本地时区：Asia/Shanghai
当前日期和时间：2021-09-30T11:30:42.314
```

1. 修改系统变量user.timezone的值

```java
public class DateTimeTest {

    public static void main(String[] args) {
        System.setProperty("user.timezone", "Europe/Paris");
        System.out.println("当前本地时区："+ ZoneId.systemDefault());
        System.out.println("当前日期和时间：" + LocalDateTime.now());
    }
}

// 控制台打印
当前本地时区：Europe/Paris
当前日期和时间：2021-09-30T05:30:58.555
```

2. 修改TimeZone的默认值

```text
public class DateTimeTest {

    public static void main(String[] args) {
        TimeZone.setDefault(TimeZone.getTimeZone("Europe/Paris"));
        System.out.println("当前本地时区："+ ZoneId.systemDefault());
        System.out.println("当前日期和时间：" + LocalDateTime.now());
    }
}

// 控制台打印
当前本地时区：Europe/Paris
当前日期和时间：2021-09-30T05:33:18.201
```

上海和巴黎的时区相差6个小时，足见修改是生效的。

原理：

LocalDateTime的API底层最终会调用到TimeZone.java的getDefault方法，getDefault最终又会调用*getDefaultRef方法，那么看下改方法，原理看注释*

```java
/**
     * Returns the reference to the default TimeZone object. This
     * method doesn't create a clone.
     */
    static TimeZone getDefaultRef() {
         // 第二种方案就是为defaultTimeZone这个变量设置了值
        TimeZone defaultZone = defaultTimeZone; 
        if (defaultZone == null) {
            // Need to initialize the default time zone.
            // 第一种方案就是在setDefaultZone方法中读取了user.timezone系统变量的值
            defaultZone = setDefaultZone();
            assert defaultZone != null;
        }
        // Don't clone here.
        return defaultZone;
    }
```

编辑于 2021-09-30 11:46
