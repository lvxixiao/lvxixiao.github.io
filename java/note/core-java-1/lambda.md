# lambda

## lambda表达式的语法（P232-234）

lambda表达式的形式：参数，箭头 -&gt; 以及一个表达式。如果代码要完成的计算无法放在一个表达式中，就可以像写方法一样，把这些代码放在{}中，并包含显式的return语句。

```text
(String first,String second) -> {
    if(first.length() < second.length()) 
        return -1;
    else if(first.length() > second.length())
        return 1;
    else
        return 0;
}
```

即使lambda表达式没有参数，仍然要提供空括号，就像无参数方法一样。

```text
() -> {
    for(int i = 100; i >= 0 ; i--)
        System.out.println(i);
}
```

如果可以推导出一个lambda表达式的参数类型，则可以忽略其类型。

```text
Comparator<String> comp = 
(first,second) -> first.length() - second.length();
```

在这里，编译器可以推导出first和second必然是字符串，因为这个lambda表达式将赋给一个字符串比较器。

如果方法只有一个参数，而且这个参数的类型可以推导得出，那么甚至还可以省略小括号

```text
//此处代码等同于新建一个匿名内部类
ActionListener listener = event ->
System.out.println("The time is " + new Date());
//Instead of (event) -> ... or (ActionEvent event) ->...
```

## 函数式接口（P234-235）

对于只有一个抽象方法的接口，需要这种接口的对象时，就可以提供一个lambda表达式。这种接口称为函数式接口\(functional inerface\)。

```text
//Comparator就是只有一个方法的接口，所以可以提供一个lambda表达式

Arrays.sort(words,
(first,second) -> first.length() - second.length())
```

在底层,Arrays.sort方法会接收实现了Comparator的某个类的对象。在这个对象上调用compare方法会执行这个lambda表达式的体。这些对象和类的管理完全取决于具体实现，与使用传统的内联类相比，这样可能要高效得多。最好把lambda表达式看作是一个函数，而不是一个对象。另外要接受lambda表达式可以传递到函数式接口。

## 方法引用（235-237）

表达式System.out::println是一个方法引用（method reference），它等价于lambda表达式x -&gt; System.out.println\(x\)。

从例子可以看出，要用::操作符分隔方法名与对象或类名。主要有3种情况：

* object::instanceMethod
* Class::staticMethod
* Class::instanceMethod

Math::pow等价于\(x,y\) -&gt; Math.pow\(x,y\)。

对于第三种情况，第1个参数会成为方法的目标。例如，String::compareToIgnoreCase等同于\(x,y\) -&gt; x.compareToIgnoreCase\(y\)。

可以在方法引用中使用this参数。例如,this::equals等同于 x -&gt; this.equals\(x\)。使用super也是合法的。

## 变量作用域（P237-239）

```text
public static void repatMessage(String text, int delay){
    ActionListener listener = event -> {
        System.out.println(text);
        Toolkit.getDefaultToolkit().beep();
    };
    new Timer(delay,listener).start();
}
//调用
repeatMessage("Hello",1000);
```

在这个例子中，这个lambda表达式有1个自由变量test。表达lambda表达式的数据结构必须存储自由变量的值，在这里就是字符串"Hello"。我们说它被lambda表达式捕获\(captured\)。关于代码块以及自由变量值有一个术语：闭包\(closure\)。

下面来看具体的实现细节，例如，可以把一个lambda表达式转换为包含一个方法的对象，这样自由变量就会复制到这个对象的实例中。

可以看到，lambda表达式可以捕获外围作用域中变量的值。在Java中，要确保所捕获的值是明确定义的，这里有一个重要的限制。在lambda表达式中，只能引用值不会改变的变量。

```text
public static void countDown(int start , int delay){
    ActionListener listener = event -> {
        start--;//Error: Can't mutate captured variable
        System.out.println(start);
    };
    new Timer(delay, listener).start();
}
```

之所以有这个限制是有原因的。如果在lambda表达式中改变变量，并发执行多个动作时就会不安全。

另外如果在lambda表达式中引用变量，而这个变量可能在外部改变，这也是不合法的。

```text
public static void repeat(String text, int count){
    for(int i = 1; i <= count ; i++){
        ActionListener listener = event -> {
            System.out.println(i + ": " + text);
            //Error: Cannot refer to changing i
        }
        new Timer(1000,listener).start();
    }
}
```

lambda表达式中捕获的变量必须实际上是最终变量\(effectively final\)。实际上的最终变量是指，这个变量初始化之后不会再为它赋新值。

lambda表达式的体与嵌套块有相同的作用域。lambda表达式中不能有同名的局部变量。在一个lambda表达式中使用this关键字时，是指创建这个lambda表达式的方法的this参数。

```text
public class Application(){
    public void init() {
        ActionListener listener = event -> {
            ActionListener listener = event -> {
                System.out.println(this.toString());
            }
        }
    }
}
```

表达式this.toString\(\)会调用Application对象的toString方法，而不是ActionListener实例的方法。

## 处理lambda表达式（P239-241）

使用lambda表达式的重点是延迟执行（deferred execution）。

如果你设计你自己的接口，其中只有一个抽象方法，可以用@FunctionalInterface注解来标记这个接口。这样做有两个优点，如果你无意中增加了另一个非抽象方法，编译器会产生一个错误信息。另外javadoc页里会指出你的接口是一个函数式接口。

**常用的函数式接口**

| 函数式接口 | 参数类型 | 返回类型 | 抽象方法名 | 描述 | 其他方法 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Runnable | 无 | void | run | 作为无参数或返回值的动作运行 |  |
| Supplier | 无 | T | get | 提供一个T类型的值 |  |
| Consumer | T | void | accept | 处理一个T类型的值 | andThen |
| BiConsumer | T,U | void | accept | 处理T和U类型的值 | andThen |
| Function | T | R | apply | 有一个T类型参数的函数 | compose,andThen,identity |
| BiFunction | T,U | R | apply | 有T和U类型的函数 | andThen |
| UnaryOperator | T | T | apply | 类型T上的一元操作符 | compose,andThen,identity |
| BinaryOperator | T,T | T | apply | 类型T上的二元操作符 | andThen,maxBy,minBy |
| Perdicate | T | boolean | test | 布尔值函数 | and,or,negate,isEqual |
| BiPredicate | T,U | boolean | test | 有两个参数的布尔值函数 | and,or,negate |

**基本类型的函数式接口**

| 函数式接口 | 参数类型 | 返回类型 | 抽象方法名 |
| :--- | :--- | :--- | :--- |
| BooleanSupplier | none | boolean | getAsBoolean |
| PSupplier | none | p | getAsP |
| PConsumer | p | void | accept |
| ObjPConsumer | T,p | void | accept |
| PFuntion | p | T | apply |
| PToQfunction | p | q | applyAsQ |
| ToPFunction | T | p | applyAsP |
| ToPBiFunction | T,U | p | applyAsP |
| PUnaryOperator | p | p | applyAsP |
| PBinaryOperator | p,p | p | applyAsp |
| PPredicate | p | boolean | test |

