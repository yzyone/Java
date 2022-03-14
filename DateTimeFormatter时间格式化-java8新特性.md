# DateTimeFormatter时间格式化-java8新特性

在java8之前我们都用SimpleDateFormat类来进行时间格式化，但这种存在一些缺点：

- 1.每处理一次时间都new一个SimpleDateFormat实例对象会占用大量的内存和jvm空间
- 2.为了减少内存开销，我们可以使用static将其设置为共享变量，但SimpleDateFormat是线程不安全的，需要通过加锁的方式来解决
 
java8之后为我们提供了DateTimeFormatter类代替SimpleDateFormat，这是一个线程安全的格式化工具类。具体使用如下：


```
//字符串转换为日期
String dateStr= "2018年12月18日";
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日");
LocalDate date= LocalDate.parse(dateStr, formatter);

//日期转换为字符串
LocalDateTime now = LocalDateTime.now();
DateTimeFormatter format = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss");
String nowStr = now.format(format);

Instant：瞬时实例。
LocalDate：本地日期，不包含具体时间。
LocalTime：本地时间，不包含日期。
LocalDateTime：组合了日期和时间，但不包含时差和时区信息。
ZonedDateTime：最完整的日期时间，包含时区和相对UTC或格林威治的时差。

//Date转LocalDateTime
Instant instant = date.toInstant();
ZoneId zoneId = ZoneId.systemDefault();
Date date = instant.atZone(zoneId).toLocalDateTime();

//LocalDateTime转Date
ZoneId zoneId = ZoneId.systemDefault();
ZonedDateTime zdt = localDateTime.atZone(zoneId);
LocalDateTime localDateTime = Date.from(zdt.toInstant());
```
