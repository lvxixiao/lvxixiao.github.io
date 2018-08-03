# 约简操作

## 约简操作（P28-29）

reduce方法是一种用于从流中计算某个值的通用机制。

```text
List<Integer> values = ...;
Optional<Integer> sum = values.stream().reduce((x,y) -> x + y);
```

在上面的例子中，reduce方法会计算vo + v1 + v2 +...，其中vi是流中的元素。如果流为空，那么该方法会返回一个Optional，因为没有任何有效的结果。

通常，会有一个幺元值e使得 e op x = x，可以使用这个元素作为计算的起点。例如，0是加法的幺元值。然后，可以调用第2种形式的reduce：

```text
List<Integer> values = ...;
Integer sum = values.stream().reduce(0, (x, y) -> x + y)
//Coputes 0 + v0 + v1 + v2 + ...
```

如果流为空，则会返回幺元值。

现在，假设你有一个对象流，并且想要对某些属性求和，例如字符串流中的所有字符串的长度，那么你就不能使用简单形式的reduce，而是需要\(T,T\) -&gt; T这样的函数，即引元和结果的类型相同的函数。但是在这种情况下，你有两种类型：流的元素具有String类型，而累积结果是整数。有一种形式的reduce可以处理这种情况。

首先，你需要体统一种“累积器”函数\(total, word\) -&gt; total + word.length\(\)。这个函数会反复被调用，产生累积的总和。但是，当计算被并行化时，会有多个这种类型的计算，你需要将它们的结果合并。因此，你需要提供第二个函数来执行此处理。

```text
int result = words.reduce(0 ,
    (total, word) -> total + word.length(),
    (total1, total2) -> total1 + total2);
```

* java.util.Stream 8
  * Optional reduce\(BinaryOperator accumulator\)
  * T reduce\(T identity, BinaryOperator accumulator\)
  * \ U reduce\(U identity, BiFunction accumulator, BinaryOperator\ combiner\)
    * 用给定的accumulator函数产生流中元素的累积总和。如果提供了幺元，那么第一个被累计的元素就是该幺元。如果提供了组合器，那么它可以用来将分别累积的各个部分整合成总和。
  *  Rcollect\(Supplier supplier, BiConsumer accumulator, BiConsumer combiner
    * 将元素收集到类型R的结果中。在每个部分上，都会调用supplier来提供初始结果，调用accumulatorl来交替地将元素添加到结果中，并调用combinerl来整合两个结果。

