# 基本类型流

## 基本类型流（P29-30）

针对基本类型：double、float、long、short、char、byte和boolean，流库中具有专门的类型IntStream、LongStream、和DoubleStream，用来直接存储基本类型值，而无需包装器。如果想要存储short、char、byte和boolean，可以使用IntStream，而对于float，可以使用DoubleStream。

## 创建基本类型流（P30）

**IntStream**

创建IntStream可以调用IntStream.of和Arrays.stream方法。

```text
IntStream stream = IntStream.of(1,1,2,3,5);
stream = Array.stream(values, from, to);//values is an int[] array
```

与对象流一样，还可以使用静态的generate和iterate方法。此外，IntStream和LongStream有静态方法range和rangeClosed，可以生成步长为1的整数范围：

```text
//Upper bound is excluded
IntStream zeroToNinetyNine = IntStream.range(0,100);
//Upper bound is included
IntStream zeroToHundred = IntStream.rangeClosed(0,100);
```

CharSequence接口拥有codePoints和chars方法，可以生成由字符的Unicode码或由UTF-16编码机制的码元构成的IntStream。

```text
String sentence = "Hello Word";
IntStream codes = sentence.codePoints();
```

当你有一个对象流时，可以用mapToInt、mapToLong和mapToDouble将其转换为基本类型流。例如，如果你有一个字符串流，并想将其长度处理为整数，那么就可以在IntStream中实现此目的：

```text
Stream<String> words = ...;
IntStream lengths = words.mapToInt(String::length);
```

将基本类型流转换为对象流，可以使用boxed方法：

```text
Stream<Integer> integers = IntStream.range(0,100).boxed();
```

Random类具有ints、longs和doubles方法，它们会返回由随机数构成的基本类型游。

## 基本类型流与对象流的主要差异（P30）

* toAray方法会返回基本类型数组。
* 产生可选结果的方法会返回一个OptionalInt、OptionalLong或OptionalDouble。这些类与Optional类类似，但是具有getAsInt、getAsLong和getAsDouble方法，而不是get方法。
* 具有返回总和、平均值、最大值和最小值的sum、average、max和min方法。对象流没有定义这些方法。
* summaryStatistics方法会产生一个类型为IntSummaryStatistics、LongsummaryStatistics或DoubleSummaryStatistics的对象，它们可以同时报告流的总和、平均值、最大值和最小值。

