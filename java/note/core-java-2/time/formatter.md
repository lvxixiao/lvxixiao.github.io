# 格式化和解析

## DateTimeFormatter

DateTimeFormatter类提供了三种用于打印日期/时间值的格式器。

* 预定义的格式器
* Locale相关的格式器
* 带有定制模式的格式器

## 预定义的格式器

标准格式器主要是为了机器刻度的时间戳而设计。

<table>
  <thead>
    <tr>
      <th style="text-align:left">格式器</th>
      <th style="text-align:left">描述</th>
      <th style="text-align:left">示例</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">BASIC_ISO_DATE</td>
      <td style="text-align:left">年、月、日、时区偏移量，中间没有分隔符</td>
      <td style="text-align:left">19690716-0500</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>ISO_LOCAL_DATE,ISO_LOCAL_TIME,</p>
        <p>ISO_LOCAL_DATE_TIME</p>
      </td>
      <td style="text-align:left">分隔符为-、:、T</td>
      <td style="text-align:left">1969-07-16，09：32：00，1969-07-16T09：32：00</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>ISO_OFFSET_DATE,ISO_LOCAL_TIME,</p>
        <p>ISO_OFFSET_DATE_TIME</p>
      </td>
      <td style="text-align:left">类似ISO_LOCAL_XXX,但是有时区偏移量</td>
      <td style="text-align:left">1690-07-16-05:00,09:32:00-05:00,1969_07_16T09:32:00-05:00</td>
    </tr>
    <tr>
      <td style="text-align:left">ISO_ZONED_DATE_TIME</td>
      <td style="text-align:left">有时区偏移量和时区ID</td>
      <td style="text-align:left">1069-07-16T09:32:00-05:00[America/New_York]</td>
    </tr>
    <tr>
      <td style="text-align:left">ISO_INSTANT</td>
      <td style="text-align:left">在UTC中，用Z时区ID来表示</td>
      <td style="text-align:left">1969-07-16T14:32:00Z</td>
    </tr>
    <tr>
      <td style="text-align:left">ISO_DATE,ISO_TIME,ISO_DATE_TIME</td>
      <td style="text-align:left"></td>
      <td style="text-align:left">1969-07-16-05:00,09:32:00-05:00,1969_07_16T09:32:00-05:00[America/New_York]</td>
    </tr>
    <tr>
      <td style="text-align:left">ISO_ORDINAL_DATE</td>
      <td style="text-align:left">LocalDate的年和年日期</td>
      <td style="text-align:left">1969-197</td>
    </tr>
    <tr>
      <td style="text-align:left">ISO_WEEK_DATE</td>
      <td style="text-align:left">LocalDate的年、星期和星期日期</td>
      <td style="text-align:left">1969-W29-3</td>
    </tr>
    <tr>
      <td style="text-align:left">RFC_1123_DATE_TIME</td>
      <td style="text-align:left">用于邮件时间戳的标准，编纂于RFC822，并在RFC1123中将年份更新到4位</td>
      <td style="text-align:left">Wed, 16 Jul 1969 09:32:00 -0500</td>
    </tr>
  </tbody>
</table>要用标准的格式器，可以直接调用其format方法

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

<table>
  <thead>
    <tr>
      <th style="text-align:left">时间的域或目的</th>
      <th style="text-align:left">示例</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">ERA</td>
      <td style="text-align:left">G: AD, GGGG: Anno Domini, GGGGG: A</td>
    </tr>
    <tr>
      <td style="text-align:left">YEAR_OF_ERA</td>
      <td style="text-align:left">yy: 69, yyyy: 1969</td>
    </tr>
    <tr>
      <td style="text-align:left">MONTH_OF_YEAR</td>
      <td style="text-align:left">M: 7,MM: 07,MMM: Jul, MMMM:July, MMMMM: J</td>
    </tr>
    <tr>
      <td style="text-align:left">DAY_OF_MONTH</td>
      <td style="text-align:left">d: 6, dd: 06</td>
    </tr>
    <tr>
      <td style="text-align:left">DAY_OF_WEEK</td>
      <td style="text-align:left">e: 3, E: Wed, EEEE: Wednesday, EEEEE: W</td>
    </tr>
    <tr>
      <td style="text-align:left">HOUR_OF_DAY</td>
      <td style="text-align:left">H: 9, HH: 09</td>
    </tr>
    <tr>
      <td style="text-align:left">CLOCK_HOUR_OF_AM_PM</td>
      <td style="text-align:left">k: 0, kk: 09</td>
    </tr>
    <tr>
      <td style="text-align:left">AMPM_OF_DAY</td>
      <td style="text-align:left">a: AM</td>
    </tr>
    <tr>
      <td style="text-align:left">MINUTE_OF_HOUR</td>
      <td style="text-align:left">mm: 02</td>
    </tr>
    <tr>
      <td style="text-align:left">SECOND_OF_MINUTE</td>
      <td style="text-align:left">ss: 00</td>
    </tr>
    <tr>
      <td style="text-align:left">NANO_OF_SECOND</td>
      <td style="text-align:left">nnnnnn: 000000</td>
    </tr>
    <tr>
      <td style="text-align:left">时区ID</td>
      <td style="text-align:left">VV: America/New_York</td>
    </tr>
    <tr>
      <td style="text-align:left">时区名</td>
      <td style="text-align:left">z: EDT, zzzz: Eastern Daylight Time</td>
    </tr>
    <tr>
      <td style="text-align:left">时区偏移量</td>
      <td style="text-align:left">x: -04, xx: -0400, xxx: -04:00, XXX: 与xxx相同，但是Z表示0</td>
    </tr>
    <tr>
      <td style="text-align:left">本地化的时区偏移量</td>
      <td style="text-align:left">
        <p>O: GMT-4, OOOO: GMT-04:00</p>
        <p>DateTimeFormatter类提供了三种用于打印日期/时间值的格式器。</p>
        <ul>
          <li>预定义的格式器</li>
          <li>Locale相关的格式器</li>
          <li>带有定制模式的格式器</li>
        </ul>
        <p>标准格式器主要是为了机器刻度的时间戳而设计。</p>
        <p>要用标准的格式器，可以直接调用其format方法</p>
        <p>静态方法ofLocalizedDate、ofLocalizedTime和ofLocalizedDateTime可以创建这种格式器</p>
      </td>
    </tr>
  </tbody>
</table>