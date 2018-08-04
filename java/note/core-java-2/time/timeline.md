# 时间线

#### 时间线\(P288\)

Java的Data和Time API规范要求Java的使用尺度为

* 每天86400秒
* 每天正午与官方时间精确匹配
* 在其他时间点上，以精确定义的方式与官方时间接近匹配

#### Instant\(P289\)

在Java中，Instant表示时间线上的某个点。被称为“新纪元”的时间远点被设置为穿过伦敦格林威治皇家天文台的本初子午线所处时区的1970年1月1日的午夜。与UNIX/POSIX时间中使用的惯例相同。

静态方法调用Instant.now\(\)会给出当前的时刻。可以使用**equals**和c**ompareTo**方法来比较两个Instant对象，因此可以将Instant对象用作时间戳。

为了得到两个时刻之间的时间差，可以使用静态方法Duration.between。

```text
Instant start = Instant.now();
runAlgorithm();
Instant end = Instant.now();
Duration timeElapsed = Duration.between(start,end);
long millis = timeElapsed.toMillis();
```

Duration是两个时刻之间的时间量，可以通过调用toNanos、toMillis、getSeconds、toMinutes、toHours和toDays来获得Duration按照传统单位度量的时间长度。

#### Instant和Duration的常用方法\(P289-290\)

| 方法 | 描述 |
| :--- | :--- |
| plus,minus | 在当前的Instant或Duration上加上或减去一个Duration |
| plusNanos,plusMillis,plusSeconds,minusNanos,minusMillis,minusSeconds | 在当前的Instant或Duration上加上或减去给定时间单位的数值 |
| plusMinutes,plusHours,plusDays,minusMinutes,minusHours,minusDays | 在当前Duration上加上或减去给定时间单位的数值 |
| multipliedBy,dividedBy,negated | 返回当前的Duration乘以或除以给定long或-1而得到的Duration。注意，你可以缩放Duration，但是不能缩放Instant |
| isZero,isNegative | 检查当前的Duratin是否是0或负值 |

