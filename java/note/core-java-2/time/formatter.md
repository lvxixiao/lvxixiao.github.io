# 格式化和解析

## DateTimeFormatter

DateTimeFormatter类提供了三种用于打印日期/时间值的格式器。

* 预定义的格式器
* Locale相关的格式器
* 带有定制模式的格式器

## 预定义的格式器

标准格式器主要是为了机器刻度的时间戳而设计。

| 格式器 | 描述 | 示例 |
| :--- | :--- | :--- |
| BASIC\_ISO\_DATE | 年、月、日、时区偏移量，中间没有分隔符 | 19690716-0500 |
| ISO\_LOCAL\_DATE,ISO\_LOCAL\_TIME,ISO\_LOCAL\_DATE\_TIME | 分隔符为-、:、T | 1969-07-16，09：32：00，1969-07-16T09：32：00 |
| ISO\_OFFSET\_DATE,ISO\_LOCAL\_TIME,ISO\_OFFSET\_DATE\_TIME | 类似ISO\_LOCAL\_XXX,但是有时区偏移量 | 1690-07-16-05:00,09:32:00-05:00,1969\_07\_16T09:32:00-05:00 |
| ISO\_ZONED\_DATE\_TIME | 有时区偏移量和时区ID | 1069-07-16T09:32:00-05:00\[America/New\_York\] |
| ISO\_INSTANT | 在UTC中，用Z时区ID来表示 | 1969-07-16T14:32:00Z |
| ISO\_DATE,ISO\_TIME,ISO\_DATE\_TIME |  | 1969-07-16-05:00,09:32:00-05:00,1969\_07\_16T09:32:00-05:00\[America/New\_York\] |
| ISO\_ORDINAL\_DATE | LocalDate的年和年日期 | 1969-197 |
| ISO\_WEEK\_DATE | LocalDate的年、星期和星期日期 | 1969-W29-3 |
| RFC\_1123\_DATE\_TIME | 用于邮件时间戳的标准，编纂于RFC822，并在RFC1123中将年份更新到4位 | Wed, 16 Jul 1969 09:32:00 -0500 |

要用标准的格式器，可以直接调用其format方法

```text
String formatted = DateTimeFormatter.ISO_OFFSET_DATE_TIME.format(apollo11launch);
//1969-07T09:32:00-04:00
```

## Local相关的格式器

| 风格 | 日期 | 时间 |
| :--- | :--- | :--- |
| SHORT | 7/16/69 | 9:32 AM |
| MEDIUM | Jul 16, 1969 | 9:32:00 AM |
| LONG | Jyly 16,1969 | 9:32:00 AM EDT |
| FULL | Wednesday,July 16, 1969 | 9:32:00 AM EDT |

静态方法ofLocalizedDate、ofLocalizedTime和ofLocalizedDateTime可以创建这种格式器

```text
DateTimeFormatter formatter = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.LONG);
String formatted = formatter.format(apollo11launch);
//July 16, 1969 9:32:00 AM EDT
```

## 常用的日期/时间格式的格式化符号

| 时间的域或目的 | 示例 |
| :--- | :--- |
| ERA | G: AD, GGGG: Anno Domini, GGGGG: A |
| YEAR\_OF\_ERA | yy: 69, yyyy: 1969 |
| MONTH\_OF\_YEAR | M: 7,MM: 07,MMM: Jul, MMMM:July, MMMMM: J |
| DAY\_OF\_MONTH | d: 6, dd: 06 |
| DAY\_OF\_WEEK | e: 3, E: Wed, EEEE: Wednesday, EEEEE: W |
| HOUR\_OF\_DAY | H: 9, HH: 09 |
| CLOCK\_HOUR\_OF\_AM\_PM | k: 0, kk: 09 |
| AMPM\_OF\_DAY | a: AM |
| MINUTE\_OF\_HOUR | mm: 02 |
| SECOND\_OF\_MINUTE | ss: 00 |
| NANO\_OF\_SECOND | nnnnnn: 000000 |
| 时区ID | VV: America/New\_York |
| 时区名 | z: EDT, zzzz: Eastern Daylight Time |
| 时区偏移量 | x: -04, xx: -0400, xxx: -04:00, XXX: 与xxx相同，但是Z表示0 |
| 本地化的时区偏移量 | O: GMT-4, OOOO: GMT-04:00DateTimeFormatter类提供了三种用于打印日期/时间值的格式器。标准格式器主要是为了机器刻度的时间戳而设计。要用标准的格式器，可以直接调用其format方法静态方法ofLocalizedDate、ofLocalizedTime和ofLocalizedDateTime可以创建这种格式器 |

