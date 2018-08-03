# Optional

## Optional类型（P11）

Optional对象是一种包装器对象，要么包装了类型T的对象，要么没有包装任何对象。

Optional类型被当作一种更安全的方式，用来代替类型T的引用，这种引用要么引用某个对象，要么为null。但是，它只有在正确使用的情况下才会更安全。

## 如何使用Optional值（P11-12）

**有效地使用Optional的关键是要使用这样的方法：它在值不存在的情况下会产生一个可代替物，而只在值存在的情况下才会使用这个值。**

```text
//没有任何匹配时，使用默认值""
String result = optionalString.orElse("");
//The wrapped string , or "" if none.

//调用代码来计算默认值
String result = optionalString.orElseGet(
() -> Local.getDefault().getDisplayName();
//The function is only called when needed

//没有值时抛出异常
String result = optionaleString.orElseThrow(IllegalStateException::new);
//Supply a method that yields an exception object
```

ifPresent方法会接受一个函数。如果该可选值存在，那么它会被传递给该函数。否则，不会发生任何事情。当调用ifPresent时，从该函数不会返回任何值。如果想要处理函数的结果，可以使用map。

```text
optionalValue.ifPresent(result::add);

/*现在added具有三种值之一：
在optionalValue存在的情况下包装在Optional中的true或false
在optionalValue不存在的情况下的空的Optional
*/
Optional<Boolean> added = optionalValue.map(results::add);
```

* java.util.Optional 8
  * T orElse\(T other\)
    * 产生这个Optional的值，或者在该Optional为空时，产生other。
  * T orElseGet\(Supplier&lt;? extends T&gt; other\)
    * 产生这个Optional的值，或者在该Optional为空时，产生调用other的结果。
  *  T orElseThrow\(Supplier&lt;? extends x&gt; exceptionSupplier\)
    * 产生这个Optional的值，或者在该Optional为空时，抛出调用exceptionSupplier的结果。
  * void ifPresent\(Consumer&lt;? super T&gt; consumer\)
    * 如果该Optional不为空，那么就将它的值传递给consumer
  * \ Opional\ map\(Function&lt;? super T,? extends U&gt; mapper\)
    * 产生将该Optional的值传递给mapper后的结果，只要这个Optional不为空且结果不为null，否则产生一个空Optional。
  * T get\(\)
    * 产生这个Optional的值，或者在该Optional为空时，抛出一个NoSuchElementException
  * boolean isPresent\(\)
    * 如果该Optional不为空，则返回true。

## 创建Optional值（P13-15）

* java.util.Optional 8
  * static  Optional of\(T value\)
  * static  Optional ofNullable\(T value\)
    * 产生一个具有给定值的Optional。如果value为null，那么第一个方法会抛出一个NullPointerException对象，第二个返方法会产生一个空Optional。
  * static  Optional empty\(\)
    * 产生一个空Optional。
  * \ Optional\ flatMap\(Function&lt;? super T, Optional\&gt; mapper\)
    * 产生将mapper应用于当前的Optional值所产生的结果，或者在当前Optional为空时，返回一个空Optional。

