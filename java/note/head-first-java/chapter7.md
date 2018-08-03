# Chapter7.Better Living in Objectville

## Question

* The JVM starts walking up the inheritance tree,

  starting at the class type you invoked the method on. But what

  happens if the JVM doesn’t ever find a match?

  * The compiler guarantees that a particular method is callable for a specific reference type,but it doesn’t say \(or care\) from which class that method actually comes from at runtime.Remember that if a class inherits a method, it has the method.Where the inherited method is defined makes no difference to the compiler.But at runtime, the JVM will always pick the right one. And the right one means, the most specific version for that particular object.

## 问题

* 虚拟机从你调用方法的类开始向上搜索继承树。当JVM没有找到会发生什么？
  * 编译器通过特别的引用类型来保证特定的方法会被调用，在运行时它不会在乎该方法实际上在哪个类找到的。记住，如果类继承一个方法，它便有了那个方法。该方法在哪里被声明对于编译器没有区别。但在运行时，JVM总是会找到正确的方法。正确的方法意思是最接近该类型的版本。

