# 日期调整期

#### TemporalAdjusters

TemporalAdjusters类提供了大量用于常见调整的静态方法。你可以将调整方法的结果传递给with方法。

```text
//某个月的第一个星期二
LocalDate firstTuesday = LocalDate.of(year,month,1).with(
    TeporalAdjusters.nextOrSame(DayOfWeek.TUESDAY);
);
```

#### TemporalAdjusters类中的日期调整期

<table>
  <thead>
    <tr>
      <th style="text-align:left">方法</th>
      <th style="text-align:left">描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">next(weekday),previous(weekday)</td>
      <td style="text-align:left">下一个或上一个给定的星期日期</td>
    </tr>
    <tr>
      <td style="text-align:left">nextOrSame(weekday),previousOrSame(weekday)</td>
      <td style="text-align:left">从给定的星期开始的下一个或上一个给定的星期日期</td>
    </tr>
    <tr>
      <td style="text-align:left">dayOfWeekInMonth(n,weekday)</td>
      <td style="text-align:left">月份中的第n个wekkday</td>
    </tr>
    <tr>
      <td style="text-align:left">lastInMonth(weekday)</td>
      <td style="text-align:left">月份中的最后一个weekday</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>firstDayOfMonth(),firstDayOfNextMonth(),firstDayOfNextYear(),</p>
        <p>lastDayOfMonth(),lastDayOfYear()</p>
      </td>
      <td style="text-align:left">方法名所描述的日期</td>
    </tr>
  </tbody>
</table>还可以通过实现TemporalAdjuster接口来创建自己的调整期。

```text
//计算下一个工作日
TemporalAdjuster NEXT_WORKDAY = w -> {
  LocalDate result = (LocalDate) w;
  do {
      result = result.plusDays(1);
  }while(result.getDayOfWeek().getValue() >= 6);
  return result;
};
LocalDate backToWork = today.with(NEXT_WORKDAY);
```

注意,lambda表达式的参数类型为Temporal，它必须被强制转型为LocalDate。你可以用ofDateAdjuster方法来避免转型，该方法期望得到的参数类型为UnaryOperator的lambda表达式。

```text
TemporalAdjuster NEXT_WORKDAY = TemporalAdjusters.ofDateAdjuster( w -> {
    LocalDate result = w;
    do{
        retult = result.plusDays(1);
    }while(result.getDayOfWeek().getValue() >= 6);
    return result;
});
```

