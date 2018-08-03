# 代理

## 代理类（P259）

代理类可以在运行时创建全新的类。这样的代理类能够实现指定的接口。尤其是，它具有下列方法：

* 指定接口所需要的全部方法
* Object类中的全部方法

  然而，不能在运行时定义这些方法的新代码。而是要提供一个调用处理器（invocation handler）。调用处理器是实现了InvocationHandler接口的类对象。在这个接口中只有一个方法： 

object invoke\(Object proxy,Method method,Object\[\] args\)

无论何时调用代理对象的方法，调用处理器的invoke方法都会被调用，并向其传递Method对象和原始的调用参数。调用处理器必须给出处理调用的方式。

## 代理类的特性（P262-263）

代理类实在程序运行过程中创建的。然而，一旦被创建，就变成了常规类，与虚拟机中的任何其他类没有什么区别。

所有的代理类都扩展于Proxy类。一个代理类只有一个实例域--调用处理器，它定义在Proxy的超类中。为了履行代理对象的职位责，所需要的任何附加数据都必须存储在调用处理器中。

所有的代理类都覆盖了Object类中的方法toString、equals、和hashCode。如果所有的代理方法一样，这些方法仅仅调用了处理器的invoke。

对于特定的类加载器和预设的一组接口来说，只能有一个代理类。也就是说，如果使用同一个类加载器和接口数组调用两次newProxyInstance方法的话，那么只能够得到同一个类的两个对象，也可以利用getProxyClass方法获得这个类

Class proxyClass = Proxy.getProxyClass\(null,interfaces\);

可以通过调用Proxy类中的isProxyClass方法检测一个特定的Class对象是否代表一个代理类。

* java.lang.reflect.InvocationHandler 1.3
  * Object invoke\(Object proxy,Method method , Object\[\] args\)
    * 定义了代理对象调用方法时希望执行的动作。
* java.lang.reflect.Proxy 1.3
  * static Class&lt;?&gt; getProxyClass\(ClassLoader loader, Class&lt;?&gt;... interfaces\)
    * 返回实现指定接口的代理类
* static Object newProxyInstance\(ClassLoader loader, Class&lt;?&gt;\[\] interfaces, InvocationHandler handler\)
  * 构造实现指定接口的代理类的一个新实例。所有方法都会调用给定处理器对象的invoke方法。
  * ClassLoader
    * 类加载器。null表示使用默认的类加载器。
  * Class&lt;?&gt;\[\]
    * Class对象数组，每个元素都是需要实现的接口。
  * InvocationHandler
    * 一个调用处理器。
* static boolean isProxyClass\(Class&lt;?&gt; cl\)
  * 如果cl是一个代理类则返回true。

