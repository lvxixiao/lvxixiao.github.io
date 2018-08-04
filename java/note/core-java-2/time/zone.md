# 时区时间

#### 夏令时

夏时令（Daylight Saving Time：DST），又称“日光节约时制”和“夏令时间”，是一种为节约能源而人为规定地方时间的制度，在这一制度实行期间所采用的统一时间称为“夏令时间”。一般在天亮早的夏季人为将时间调快一小时，可以使人早起早睡，减少照明量，以充分利用光照资源，从而节约照明用电。各个采纳夏时制的国家具体规定不同。目前全世界有近110个国家每年要实行夏令时

注:中国没有夏令时与冬令时的概念，不需要调整时间。

#### 时区ID

每个时区都有一个ID，例如America/New\_York和Europe/Berlin。要想找出所有可用的时区，可以调用ZoneId,getAvailableZoneIds。

#### ZonedDateTime

给定一个时区ID，静态方法ZoneId.of\(id\)可以产生一个ZoneId对象。可以通过调用local.atZone\(ZoneId\)用这个对象将LocalDateTime对象转换为ZonedDateTime对象，或者可以通过调用静态方法ZonedDateTime.of\(year,month,day,hour,minute,second,nano,zoneId\)来构造一个ZonedDateTime对象。

```text
ZonedDateTime time = ZonedDateTime.of(1969,7,16,9,32,0,0,ZoneId.of("America/New_York"))
```

调用time.toInstant可以获得对应的Instant对象。反过来，如果你有一个时刻对象，调用instant.atZone\(zoneId.of\("UTC"\)\)可以获得格林威治皇家天文台的ZonedDateTime对象，或者使用其他的ZoneId获得地球上其他地方的ZoneId。

ZonedDateTime的许多方法都与LocalDateTime的方法相同，它们大多数都很直接，但是夏令时带来了一些复杂性。

当夏令时开始时，时钟要向前拨快一小时。例如，在2013年，中欧地区在3月31日2：00切换到夏令时，如果你试图构建的时间是不存在的3月31日2：30，那么你实际得到的是3：30.

```text
ZonedDateTime skipped = ZonedDateTime.of(
    LocalDate.of(2013,3,31),
    LocalTime.of(2,30),
    ZoneId.of("Europe/Berlin"));
    //Constructs March 31 3:30
```

反过来，当夏令时结束时，时钟要向回拨慢一小时，这样同一个本地时间就会有出现两次。当你构建位于这个时间段内的时间对象，就会得到这两个时刻中较早的一个。

```text
ZonedDateTime ambiguous = ZonedDateTime.of(
    LocalDate.of(2013,10,27),
    LocalTime.of(2,30),
    ZoneId.of("Europe/Berlin"));
    //2013-10-27t02:30+02:00[Europe/Berlin]
    ZonedDateTime anHourLater = ambiguous.plusHours(1);
    //2013-10-27t02:30+01:00[Europe/Berlin]
```

你还需要在调整跨越夏令时边界的日期时特别注意。例如，如果你将会议设置在下个星期，不要直接加上一个7天的Duration。而是应该使用period类。

```text
ZonedDaTEtIME nextMeeting = meeting.plus(Period.ofDays(7));
```

