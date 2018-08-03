# 流

## 什么是流（P1-2）

流提供了一种让我们可以在比集合更高的概念级别上指定计算的数据视图。通过使用流，我们可以说明想要完成什么任务，而不是说明如何去实现它。

流遵循了“做什么而非怎么做”的原则

## 流与集合的差异（P2）

* 流并不存储元素，这些元素可能存储在底层的集合中，或者是按需生成的。
* 流的操作不会修改其数据源。
* 流的操作是尽可能惰性执行的。这意味着直至需要其结果时，操作才会执行。

## 流的创建（P3-6）

Stream.of方法接受可变长参数

```text
Stream<String> song = Stream.of("gently","down","the","stream");
```

Stream接口有两个用于创建无限流的静态方法。generate方法接收一个不包含任何引元的函数\(Supplier\)。只要需要一个流类型的值，该函数就会被调用以产生一个这样的值。

```text
Stream<String> echos = Stream.generate(() -> "Echo");

Stream<Double> randoms = Stream.generate(Math::random);
```

为了产生无限序列，例如0 1 2 3 ...，可以使用iterate方法。它会接受一个“种子”值，以及一个函数\(UnaryOperation\)，并且会反复地将该函数应用到之前的结果上。

```text
Steram<BigInteger> integers = Stream.iterate(BigInteger.ZERO,
n -> n.add(BigInteger.ONE));
```

* java.util.stream.Stream 8
  * static  Stream of\(T... values\)
    * 产生一个元素为给定值的流。
  * static  Stream empty\(\)
    * 产生一个不包含任何元素的流
  * static  Stream generate\(Supplier s\)
    * 产生一个无限流，它的值是通过反复调用函数s而构建的。
  * static  Stream iterate\(T seed, UnaryOperator f\)
    * 产生一个无限流，它的元素包含种子，在种子上调用f产生的值、在前一个元素上调用f产生的值，等等。
* java.util.Arrays 1.2
  * static  Stream stream\(T\[\] array, int startInclusive, int endExclusive\) 8
    * 产生一个流，它的元素由数组中指定范围内的元素构成。
* java.util.regex.Pattern 1.4
  * Stream splitAsStream\(CharSequence input\) 8
    * 产生一个流，它的元素是输入中由该模式界定的部分。
* java.nio.file.Files 7
  * static Stream lines\(Path path\) 8
  * static Stream lines\(Path path, Charset cs\) 8
    * 产生一个流，它的元素是指定文件中的行，该文件的字符集为UTF-8，或者为指定的字符集。
* java.util.function.Supplier 8
  * T get\(\)
    * 提供一个值

## filter、map和flatMap方法（P6-8）

**filter**

filter转换会产生一个流，它的元素与某种条件相匹配。filter的引元是Predicate，即从T到boolean的函数。

```text
List<String> wordList = ...;
Stream<String> longWords = wordList.stream().filter(w -> w.length() > 12);
```

**map与flatMap**

如果我们想要按照某种方式来转换流中的值，此时，可以使用map方法并传递执行该转换的函数。

在使用map时，会有一个函数应用到每个元素上，并且其结果是包含了应用该函数后所产生的所有结果的流。

```text
public static Stream<String> letters(String s) {
    List<String> result = new ArrayList<>();
    for(int i = 0; i < s.length(); i++)
        result.add(s.substring(i,i+1));
    return result.stream();
}
//调用
Stream<Stream<String>> result = words.stream().map(w -> letters(w));
//或者
Stream<String> flatResult = words.stream().flatMap(w -> letters(w));
```

调用map会产生包含流的流，类似\[...\["y","o","u","r"\],\["b","o","a","t"\]...\]的流。

调用flatMap会产生像\[..."y","o","u","r","b","o","a","t"...\]的流。

**flatMap**

在流之外的类中，也可以发现flatMap方法。因为它是计算机科学中的一种通用概念。假设我们有一个泛型G\(例如Stream\)，以及将某种类型T转换为G的函数f和将类型U转换为G的函数g。然后，我们可以通过使用flatMap来组合它们，即首先应用f，然后应用g。

* java.util.stream.Stream 8
  * Stream filter\(Predicate&lt;? super T&gt; perdicate\)
    * 产生一个流，它包含当前流中所有满足断言条件的元素。
  *  Stream map\(Function&lt;? super T,? extends R&gt; mapper\)
    * 产生一个流，它包含将mapper应用于当前流中所有元素所产生的结果。
  *  Stream flatMap\(Function&lt;? super T, ? extends Stream&lt;? extends R&gt;&gt; mapper\)
    * 产生一个流，它是通过将mapper应用于当前流中所有元素所产生的结果连接到一起而获得的。\(这里的每个结果都是一个流\)

## 抽取子流和连接流（P8）

调用stream.limit\(n\)会返回一个新的流，它在n个元素之后结束\(如果原来的流更短，那么就会在流结束时结束\)。这个方法对于剪裁无限流的尺寸会显得特别有用。

```text
//产生一个包含100个随机数的流
Stream<Double> randoms = Stream.generate(Math::random).limit(100);
```

调用stream.skip\(n\)正好相反：它会丢弃前n个元素。

```text
Stream<String> words = Stream.of(contents.split("\\PL+")).skip(1);
```

Stream类的静态concat方法可以将两个流连接起来。

```text
Stream<String> combined = Stream.concat(letters("Hello"), letters("World"));
//Yields the stream["H","e","l","l","o","W","o","r","l","d"];
```

* java.util.stream.Stream 8
  * Stream limit\(long maxSize\)
    * 产生一个流，其中包含了当前流中最初的maxSize个元素
  * Stream skip\(long n\)
    * 产生一个流，它的元素是当前流中除了前n个元素之外的所有元素。
  * static  Stream concat\(Stream&lt;? extends T&gt; a, Stream&lt;? extends T&gt; b\)
    * 产生一个流，它的元素是a的元素后面跟着b的元素。

## 其他的流转换（P8-9）

**distinct**

distinct方法会返回一个流，他的元素是从原有流中产生的，即原来的元素按照同样的顺序剔除重复元素后产生的。

```text
Stream<String> uniqueWords = Stream.of("merrily","merrily","merrily","merrily","gently").distinct();
//Only one "merrily" is retained.
```

**sorted**

对于流的排序，有多种sorted方法的变体可用。其中一种用于操作Cpmparable元素的流，而另一种可以接受一个Comparator。与所有的流转换一样，sorted方法会产生一个新的流。它的元素是原有流中按照顺序排列的元素。

我们在对集合排序时可以不使用流。但是，当排序处理是流管道的一部分时，sorted方法就会显得很有用。

```text
Stream<String> longestFirst = words.stream().sorted(Comparator.comparing(String::length).reversed());
```

**peek**

peek方法会产生另一个流，它的元素与原来流中的元素相同，但是在每次获取一个元素时，都会调用一个函数。这对于调试来说很方便。

```text
Object[] powers = Stream.iterate(1.0, p -> p * 2).peek(e -> System.out.println("Fetching "+ e)).limit(20).toArray();
```

* java.util.stream.Stream 8
  * Stream distinct\(\)
    * 产生一个流，包含当前流中所有不同的元素。
  * Stream sorted\(\)
  * Stream sorted\(Comparator&lt;? super T&gt; comparator
    * 产生一个流，它的元素是当前流中的所有元素按照顺序排序的。第一个方法要求元素是实现了Comparable的类的实例。
  * Stream peek\(Consumer&lt;? super T&gt; action\)
    * 产生一个流，它与当前流中的元素相同，在获取其中每个元素时，会将其传递给action。

## 简单约简（P9-10）

约简是一种终结操作\(terminal operation\)，它们会将流约简为可以在程序中使用的非流值。

* java.util.stream.Stream 8
  * Optional max\(Comparator&lt;? super T&gt; comparator\)
  * Optional min\(Comparator&lt;? Super T&gt; comparator\)
    * 分别产生这个流的最大元素和最小元素，使用由给定比较器定义的排序规则，如果这个流为空，会产生一个空的Optional对象。这些操作都是终结操作。
  * Optional findFirst\(\)
  * Optional findAny\(\)
    * 分别产生这个流的第一个和任意一个元素，如果这个流为空，会产生一个空的Optional对象。这些操作都是终结操作。
  * boolean anyMatch\(Predicate&lt;? super T&gt; predicate\)
  * boolean allMatch\(Predicate&lt;? super T&gt; predicate\)
  * boolean noneMatch\(Predicate&lt;? super T&gt; predicate\)
    * 分别在这个流中任意元素、所有元素和没有任何元素匹配给定断言时返回true。这些操作都是终结操作。

