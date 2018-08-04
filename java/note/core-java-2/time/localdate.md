# 本地时间

#### LocalDate\(P291\)

LocalDate是带有年、月、日的日期。可以使用静态方法now或of构建LocalDate对象。

```text
LocalDate today = LocalDate.now();
LocalDate birthday = LocalDate.of(1995,2,26);
```

与UNIX和java.util.Date中使用的月从0开始计算而年从1900年开始计算的不规则的惯用法不同，这里需要常规使用的月份的数字。

#### LocalDate的方法\(P291-293\)

| 方法 | 描述 |
| :--- | :--- |
| now,of | 构建一个LocalDate对象 |
| plusDays,plusWeeks,plusMonths,plusYears | 在当前的LocalDate上加上一定量的天、星期、月或年 |
| minusDays,minusWeeks,minusMonths,minusYears | 在当前的LocalDate上减去一定量的天、星期、月或年 |
| plus,minus | 加上或减去一个Duration或Period |
| withDayOfMonth,withDayOfYear,withMonth,withYear | 返回一个新的LocalDate，其月的日期、年的日期、月和年修改为给定的值。 |
| getDayOfMonth | 获取月的日期\(1~31\) |
| getDayOfYear | 获取年的日期\(1~366\) |
| getDayOfWeek | 获取星期日期，返回DayOfWeek的枚举值 |
| getMonth,getMonthValue | 获取月份的Month枚举值，或者是1~12之间的数字 |
| getYear | 获取年份 |
| until | 获取Period,或者两个日期之间按照给定的ChronoUnits计算的数值 |
| isBefore,isAfter | 将当前的LocalDate与另一个LocalDate进行比较 |
| isLeapYear | 是否闰年 |

#### 其他时间对象

除了LocalDate之外，还有MonthDay、YearMonth和Year类可以描述部分日期。

