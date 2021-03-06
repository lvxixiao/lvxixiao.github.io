# Java内存区域

## 运行时数据区域

Java虚拟机在执行Java程序的过程中会把它所管理的内存划分为若干个不同的数据区域。根据《Java虚拟机规范\(JavaSE 7版\)》的规定，Java虚拟机所管理的内存包括**方法区\(Method Area\)**，**堆\(Heap\)**，**虚拟机栈\(VM Stack\)**，**本地方法栈\(Native Method Stack\)**，**程序计数器\(Program Counter Register\)**。

## 程序计数器

程序计数器\(Program Counter Register\)是一块较小的内存空间，它可以看作是当前线程所执行的字节码的行号指示器。在虚拟机的概念模型里\(仅是概念模型，各种虚拟机可能会通过一些更高效的方式去实现\)，字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令。

由于Java虚拟机的多线程是通过线程轮流切换并分配处理器执行时间的方式来实现的，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各条线程之间计数器互不影响，独立存储，这类内存区域称为“线程私有”内存。

## 虚拟机栈

Java虚拟机栈\(Java Virtual Machine Stacks\)是线程私有的，它的生命周期与线程相同。

虚拟机栈描述的是Java方法执行的内存模型：每个方法在执行的同时都会创建一个栈帧\(Stack Frame\)用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每一个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。

当进入一个方法时，这个方法需要在帧中分配多大的局部变量空间是完全确定的，在方法运行期间不会改变局部变量表的大小。

## 本地方法栈

本地方法栈\(Native Method Stack\)与虚拟机栈所发挥的作用非常相似，它们之间的区别不过是虚拟机栈为虚拟机执行Java方法\(也就是字节码\)服务，而本地方法栈则为虚拟机使用到的Native方法服务。

有些虚拟机\(如 Sun HotSpot虚拟机\)将本地方法栈与虚拟机栈合二为一。

## 堆

对于大多数应用来说，堆\(Heap\)是Java虚拟机所管理的内存中最大的一块。堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例。

堆是垃圾收集器管理的主要区域，因此很多时候也被称做"GC堆"\(Garbage Collected Heap\)。由于现在收集器基本都采用分代收集算法，所以堆还可以细分为：新生代和老年代；再细致一点的有Eden空间、From Survivor空间、To Survivor空间等。从内存分配的角度看，线程共享的堆中可能划分出多个线程私有的分配缓冲区\(Thread Local Allocation Buffer,TLAB\)。

## 方法区

方法区\(Method Area\)与堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的类信息、常量、静态常量、即时编译器编译后的代码等数据。它又被称为Non-Heap（非堆）。

#### 注

网友说Java 1.7之后，常量池移动到了堆中.

