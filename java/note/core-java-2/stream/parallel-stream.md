# 并行流

## 创建并行流（P34）

可以使用Collection.parallelStream\(\)方法从任何集合中获取一个并行流：

```text
Stream<String> paralleWords = words.parallelStream();
```

parallel方法可以将任意的顺序流转换为并行流

```text
Stream<String> parallelWords = Stream.of(wordArray).parallel();
```

## 正确使用并行流（P34-36）

流使得并行处理块操作变得很容易，这个过程几乎是自动的，但是需要遵守一些规则。

只要在终结方法执行时，流处于并行模式，那么所有的中间操作都将被并行化。当流操作并行运行时，其目标是要让其返回结果与顺序执行时返回的结果相同。重要的是，这些操作可以任意顺序执行。

假设你想要对字符串流中的所有短单词计数

```text
//error code
int[] shortWords = new int[12];
words.parallelStream().forEach(
    s ->{ 
        if(s.length() < 12) shortWords[s.length()++]; });
System.out.println(Arrays.toString(shortWords));
```

这是一种非常糟糕的代码。传递给forEach的函数会在多个并发线程中运行，每个都会更新共享的数组。正确的代码如下：

```text
Map<Integer, Long> shortWordCounts = words.parallelStream()
    .filter(s -> s.length() < 10)
    .collect(groupingBy(String::length,counting()));
```

当放弃排序需求时，有些操作可以被更有效地并行化。通过在流上调用unordered方法，就可以明确表示我们对排序不感兴趣。Stream.distinct就是从这种方式中获益的一种操作。在有序的流中，distinct会保留所有相同元素中的第一个，这对并行化是一种阻碍，因为处理每个部分的线程在其之前的所有部分都被处理完之前，并不知道应该丢弃哪些元素。如果可以接受保留唯一元素中任意一个的做法，那么所有部分就可以并行地处理。还可以通过放弃排序要求来提高limit方法的速度。

```text
Stream<String> sample = words.parallelStream().unordered().limit(n);
```

为了让并行流正常工作，需要满足以下条件

* 数据应该在内存中，必须等到数据到达是非常低效的。
* 流应该可以被高效地分成若干子部分。由数组或平衡二叉树支撑的流都可以工作得很好，但是Stream.iterate返回的结果不行。
* 流操作的工作量应该具有较大的规模。如果总工作负载并不是很大，那么搭载并行计算时所付出的代价就没有什么意义。
* 流操作不应该被阻塞。

## 相关API（P38）

* java.util.stream.BaseStream 8
  * S parallel\(\)
    * 产生一个与当前流中元素相同的并行流
  * S unordered\(\)
    * 产生一个与当前流中元素相同的无序流
* java.util.Collection 1.2
  * Stream parallelStream\(\) 8
    * 用当前集合中的元素产生一个并行流。

