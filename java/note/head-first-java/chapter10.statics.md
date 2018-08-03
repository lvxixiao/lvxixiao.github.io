# Chapter10.statics

## BULLET POINTS

* A static method should be called using the class name rather than an object reference variable
* A static method can be invoked without any instances of the method's class on the heap.
* A static method is good for a utility method that does not\(and will never\) depend on a particular instance variable value.
* A static method is not associated with a particular instance--only the class--so it cannot access any instance variable values of its class.It wouldn't know which instance's values to use.
* A static method cannot access a non-static method,since non-static methods are usually associated with instance variable state.
* If you have a class with only static methods,and you do not want the class to be instantiated,you can mark the constructor private.
* A static variable is a variable shared by all members of a given class. There is only one copy of a static variable in a class,rather than one copy per each individual instance for instance variables.
* A static method can access a static variable.
* To make a constant in Java,mark a variable as both static and final.
* A final static variable must be assigned a value either at the time it is declared, or in a static initializer.

  ```text
  static {
    DOG_CODE = 420;
  }
  ```

* The naming convention for constants\(final static variables\) is to make the name all uppercase.
* A final variable value cannot be changed once it has been assigned.
* Assigning a value to a final instance variable must be either at the time it is declared,or in the constructor.
* A final method cannot be overridden.
* A final class cannot be extended\(subclassed\).

## 要点

* 静态方法应该通过类名调用而不是通过对象引用调用
* 当堆上没有静态方法的对象实例时，静态方法依然可以被调用。
* 静态方法是一个好的通用方法，不应该依靠特别的实例变量值。
* 静态方法与类中的实例变量没有联系，它不能使用类的任何实例变量值。
* 静态方法不能调用非静态方法。因为非静态方法与实例变量有联系。
* 如果你的类只有静态方法，并且不希望类被实例化，你可以用private修饰构造函数。
* 静态变量是可以被类的所有实例共享。静态变量所有类只有一份，而不是每个类的实例各自有一份。
* 静态方法能使用静态变量。
* 在Java中创建一个常量，必须使用static和final修饰
* 一个被final和static修饰的变量必须在声明时或者静态初始化时程序时赋值。

  ```text
  static{
    DOG_CODE = 420;
  }
  ```

* 常量的名字规范是全部大写。
* final变量值不能被修改。
* final实例变量必须在声明时或者在构造函数中赋值。
* final方法不能被覆盖。
* final类不能被继承。

