# 群组、分区、下游收集器

## 群组和分区（P23-25）

将具有相同特性的值群聚成组是非常常见的，并且groupingBy方法直接就支持它

```text
Map<String, List<Locale>> countryToLocales = locales.collect(Collectors.goupingBy(Locale::getCountry));
//函数Locale::getCountry是群组的分类函数。
//Yields locales [it_CH, de_CH, fr_CH]
List<Locale> swissLocales = countryToLocales.get("CH");
```

当分类函数是断言函数\(即返回boolean值的函数\)时，流的元素可以分区为两个列表：该函数返回true的元素和其他的元素。在这种情况下，使用partitioningBy比使用groupingBy要更高效。

```text
Map<Bolean,List<Locale>> englishAndOtherLocales = locales.collect(
Collectors.partitioningBy(l -> l.getLanguage().equals("en")));

List<Locale> englishLocales = engLishAndOtherLocales.get(true);
```

* java.util.stream.Collector 8
  * static Collector&gt;&gt; groupingBy\(Function&lt;? extend T,? extend K&gt; classifier\)
  * static Collector&gt;&gt; groupingByConcurrent\(Function&lt;? extend T,? extend K&gt; classifier\)
    * 产生一个收集器，它会产生一个映射表或并发映射表，其键是将classifier应用于所有收集到的元素上所产生的结果，而值是由具有相同键的元素构成的一个个列表。
  * static  Collector&gt;&gt; partitioningBy\(Predicate&lt;? super T&gt; predicate\)
    * 产生一个收集器，它会产生一个映射表，其键是true/false，而值是由满足/不满足断言的元素构成的列表。

## 下游收集器（P24-28）

groupingBy方法会产生一个映射表，它的每个值都是一个列表。如果想要以某种方式来处理这些列表，就需要提供一个“下游收集器”。

```text
import java.util.stream.Collectors.*

//将群组元素约简为数字的收集器
//counting会产生收集到的元素的个数
Map<String, Long> countryToLocaleCounts = locales.collect(
    groupingBy(Locale::getCountry, counting()));

//summingInt会接受一个函数作为引元，将该函数应用到下游元素中，并产生它们的和。
Map<String, Integer> stateToCityPopulation = cities.collect(
    groupingBy(City::getState, summingInt(City::getPopulation)));

//maxBy和minBy会接受一个比较器，并产生下游元素中的最大值和最小值
Map<String, Optional<City>> stateToLargestCity = cities.collect(
    groupingBy(City::getState, maxBy(Coparator.comparing(City::getPopulation))));

//mapping方法会产生将函数应用到下游结果上的收集器，并将函数值传递给另一个收集器
Map<String, Optional<String>> stateToLongestCityName =cities.collect(
    groupingBy(City::getState,
    mapping(City::getName,
    maxBy(Comparator.comparing(String::length)))));
```

将收集器组合起来是一种很强大的方式，但是它也可能会导致产生非常复杂的表达式。它们的最佳用法是与groupingBy和partitioningBy一起处理“下游的”映射表中的值。否则，应该直接在流上应用诸如map、reduce、count、max和min这样的方法。

* java.util.stream.Collectiors 8
  * static  Collector counting\(\)
    * 产生一个可以对收集到的元素进行计数的收集器。
  * static  Collector summingInt\(ToIntFunction&lt;? super T&gt; mapper\)
  * static  Collector summingLong\(ToLongFunction&lt;? super T&gt; mapper\)
  * static  Collector summingDouble\(ToDoubleFunction&lt;? super T&gt; mapper\)
    * 产生一个收集器，对将mapper应用到收集到的元素上之后产生的值计算总和。
  * static  Collector&gt; maxBy\(Comparator&lt;? super T&gt; comparator\)
  * static  Collector&gt; minBy\(Comparator&lt;? super T&gt; comparator\)
    * 产生一个收集器，使用comparator指定的排序方法，计算收集到的元素中的最大值/最小值。
  * static  Collector mapping\(Function&lt;? super T,? extends U&gt; mapper, Collector&lt;？ super U,A,R&gt; downstream\)
    * 产生一个收集器，它会产生一个映射表，其键是将mapper应用到收集到的数据上面产生的，其值是使用downstream收集器收集到的具有相同键的元素。

