# 收集结果

## 收集结果（P15-19）

当处理完流之后，可以调用iterator方法，它会产生可以用来访问元素的旧式风格的迭代器，或者调用forEach方法，将某个函数应用于每个元素。在并行流上，forEach方法会以任意顺序遍历各个元素。如果想要按照流中的顺序来处理它们，可以调用forEachOrdered方法\(这个方法会失去并行处理的部分甚至全部优势\)。

如果想要将结果收集到数据结构中，可以调用toArray，获得由流的元素构成的数组。还有一个便捷方法collect可用，它会接受一个Collector接口的实例。Collectors类提供了大量用于生成公共收集器的工厂方法。

```text
List<String> result = stream.collect(Collectors.toList());

Set<String> result = stream.collect(Collectors.toCollection(TreeSet::new));

//假设想要通过连接操作来收集流中的所有字符串，可以调用
String result = stream.collect(Collectors.joining());

//如果想要在元素之间增加分隔符，可以将分隔符传递给joining方法
String result = stream.collect(Collectors.joining(", "));
```

如果想要将流的结果约简为总和、平均值、最大值或最小值，可以使用summarizing\(Int\|Long\|Double\)方法中的某一个。这些方法会接受一个将流对象映射为数据结构的函数，算总和、数量、平均值、最小值和最大值。

```text
IntSummaryStatistics summary = stream.collect(Collectors.summarizingInt(String::length));

double averageWordLength = summary.getAverage();
double maxWordLength = summary.getMax();
```

* java.util.stream.BaseStream 8
  * Iterator iterator\(\)
    * 产生一个用于获取当前流各个元素的迭代器。这是一种终结操作。
* java.util.stream.Stream 8
  * void forEach\(Consumer action\)
    * 在流的每个元素上调用action。这是一种终结操作。
  * Object toArray\(\)
  *  A\[\] toArray\(IntFunction generator\)
    * 产生一个数组对象，或者在将引用A\[\]::new传递给构造器时，返回一个A类型的数组。这些操作都是终结操作。
  *  R collect\(Collector collector\)
    * 使用给定的收集器来收集当前流中的元素。Collectors类有用于多种收集器的工厂方法。
* java.util.stream.Collectors 8
  * static  Collector&gt; toList\(\)
  * static  Collector&gt; toSet\(\)
    * 产生一个将元素收集到列表或集中的收集器。
  * static  Collector toCollection\(Supplier collectionFactory\)
    * 产生一个将元素收集到任意集合中的收集器。可以传递一个诸如TreeSet::new的构造器引用。
  * static Collector joining\(\)
  * static Collector joining\(CharSequence delimiter\)
  * static Collector joining\(CharSequence delimiter,CharSequence prefix, CharSequence suffix\)
    * 产生一个连接字符串的收集器。分隔符会置于字符串之间，而第一个字符串之前可以有前缀，最后一个字符串之后可以有后缀。如果没有指定，那么他们都为空。
  * static  Collector summarizingInt\(ToIntFunction mapper\)
  * static  Collector summarizingLong\(ToIntFunction mapper\)
  * static  Collector summarizingDouble\(ToIntFunction mapper\)
    * 产生能够生成\(Int\|Long\|Double\)SummaryStatistics对象的收集器，通过它可以获得将mapper应用于每个元素后产生的结果的个数、总和、平均值、最大值和最小值。
* IntSummaryStatistics 8
* LongSummaryStatistics 8
* DoubleSummaryStatistics 8
  * long getCount\(\)
    * 产生汇总后的元素的个数。
  * \(int\|long\|double\) getSum\(\)
  * double getAverage\(\)
    * 产生汇总后的元素的总和或平均值，或者在没有任何元素时返回0.
  * \(int\|long\|double\) getMax\(\)
  * \(int\|long\|double\) getMin\(\)
    * 产生汇总后的元素的最大值和最小值，或者在没有任何元素时，产生\(Integer\|Long\|Double\).\(MAX\|MIN\)\_VALUE。

## 收集到映射表中（P19-23）

Collectors.toMap方法有两个函数引元，它们用来产生映射表的键和值。

```text
Map<Integer, String> idToName = people.collect(Collectors.toMap(Person::getId, Person::getName));
```

如果有多个元素具有相同的键，那么就会存在冲突，收集器将会抛出一个IllegalStateException对象。可以通过提供第3个函数引元来覆盖这种行为，该函数会针对给定的已有值和新值来解决冲突并确定键对应的值。这个函数应该返回已有值、新值或它们的组合。

```text
Stream<Locale> locales = Stream.of(Locale.getAvailableLocales());
Map<String, String> languageNames = locales.collect(
    Collectors.toMap(
        Locale::getDisplayLanguage,
        l -> l.getDisplayLanguage(l),
        (existingValue, newValue) -> existingValue));
```

对于每一个toMap方法，都有一个等价的可以产生并发映射表的toConcurrentMap方法。单个并发映射表可以用于并行集合处理。当使用并行流时，共享的映射表比合并映射表更高效。注意，元素不再是按照流中的顺序收集的。

