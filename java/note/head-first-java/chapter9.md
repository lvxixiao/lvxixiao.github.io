# Chapter9.constructors and garbage collection

## The Stack and the Heap

In java,we care about two areas of memory--the one where objects live\(the heap\),and the one where method invocations and local variables live\(the stack\).When a JVM starts up,it gets a chunk of memory from the underlying OS,and uses it to run your Java program.

## 栈和堆

在Java里，我们关系两个内存区域--一个是对象生存的地方（堆），一个是方法调用与本地变量生存的地方（栈）。当虚拟机启动，它会从OS中获得一块内存，然后用来运行你的Java程序。

## Instance Variables

Instance avriables are declared inside a class but not inside a method.They represent the "fields" that each individual object has\(which can be filled with different values for each instance of the class\).Instance variables live inside the object they belong to.

## 实例变量

实例变量被声明在类里面但不在方法里。它们代表每个独立对象的“字段”（每个对象的实例变量能有不同的值）。实例变量生活在所属的对象里。

## Local Variables

Local variables are declared inside a method,including method parameters.They're temporary,and live only as long as the method is on the stack\(in other words,as long as the method has not reached the closing curly brace\).

## 本地变量

本地变量被声明在方法里，包括方法参数。它们是暂时的，只生活在方法被放在栈上的时间（也就是方法调用至执行完毕之前）。

## Methods are stacked

When you call a method,the method lands on the top of a call stack.That new thing that's actually pushed onto the stack is the stack frame,and it holds the state of the method including which line of code is executiong,and the values of all local variables.

## 方法会被堆在一起

当你调用一个方法时，该方法会被放在栈的顶端。实际被推上栈的是堆栈块，它带有方法的状态，包括执行到哪一行代码以及所有本地变量的值。

## What about local variables that are objects?

a non-primitive variable holds a reference to an object,not the object itself.It doesn't matter where they're declared or created.If the local variable is a reference to an object,only the variable goes ont the stack.The object itself still goes in the heap.

## 关于引用型变量

一个非基本类型的变量只是保存对象的引用，不是对象本身。不论对象被声明或者创造，如果本地变量是对象的引用，只有变量会被放在栈上。对象只会存在于堆上。

## The role of superclass constructors in an object's life.

All the constructors in an object's inheritance tree must run when you make a new object.

That means every superclass has a constructor\(because every class has a constructor\),and each constructor up the hierarchy runs at the time an object of a subclass is created.

A subclass might inherit methods that depend on superclass state\(in other words,the value of instance variables in the superclass\).For an object to be fully-formed,and that's why the super constructor must run.All instance variables from every class in the inheritance tree have to be declared and initialized.The superclass constructor must finish before its subclass constructor.

When a constructor runs,it immediately calls its superclass constructor,all the way up the chain until you get to the class Object constructor.Even abstract classes have constructors.

## 父类构造函数在对象生命中的角色

当你创建一个对象时，所有对象继承树中的构造函数都会执行

这意味着每个父类都有构造函数（因为每个类都至少有一个构造函数），且每个构造函数都会在子类对象创建时执行。

子类必须根据父类的状态（也就是父类的实例变量）来继承方法。因为对象完整成型需要父类的实例变量，这就是父类构造函数必须执行的原因。继承树上的所有实例变量必须被声明和初始化。父类构造函数必须在子类构造函数之前执行完成。

当构造函数执行，便立即调用父类的构造函数，这回连锁反应到Object为止。即使是抽象类也有构造函数。

## this\(\) and super\(\)

* Use this\(\) to call a constructor from another overloaded constructor in the same class.
* The call to this\(\) can be used only in a constructor,and must be the first statement in a constructor.
* A constructor can have a call to super\(\) OR this\(\),but never both!

## this\(\) 和 super\(\)

* 使用this\(\)调用同一个类另一个被重载的构造函数。
* 调用this\(\)必须在构造函数中，必须是构造函数中第一行语句。
* super\(\)与this\(\)不能出现在同一个构造函数中。

## The difference between life and scope for local variables

**Life**

A local variable is alive as long as its Stack frame is on the Stack.In other words,until the method completes.

**Scope**

A local variable is in scope only within the method in which the variable was declared.When its own method calls another,the variable is alive,but not in scope until its method resumes.You can use a variable only when it is scope.

## 本地变量“life”与“scope”的差别

**Life**

只要本地变量的堆栈块还在栈上，它就算活着。也就是说，活到方法执行完毕为止。

**Scope**

本地变量的范围只限于该变量被声明的方法内。当此方法调用其它方法，本地变量活着，但是不在范围内，直到此方法继续执行。你只能在本地变量的范围内使用它。

## How does variable life affect object life?

An objecy is alive as long as there are live references to it.If a reference variable goes out of scope but is still alive,the object it refers to is still alive on the Heap.And then you have to ask..."What happens when the Stack frame holding the reference gets popped off the Stack at the end of the mehod?"

If that was the only live reference to the object,the object is now abandoned on the Heap.The trick is to know the point at which an object becomes eligible for garbage collection.

## 变量生命周期怎么影响对象生命周期

只要对象的引用活着，对象也就活着。如果引用变量超出范围但还活着，对象也会继续活在堆上。然后你会问：“当拥有引用变量的方法从栈中弹出会发生什么？”

如果这是该对象的唯一引用变量，那么对象现在会在堆中被抛弃。关键是要知道对象成为有资格被垃圾收集器回收。

