# 继承与初始化

#### 类初始化与加载\(P146-147\)

每个类的编译代码都存在于它自己的独立的文件中。该文件只在需要使用程序代码时才会被加载。这通常是指加载发生于创建类的第一个对象之时，但是当访问static域或static方法时，也会发生加载\(构造器也是static方法，尽管static关键字并没有显式地写出来。因此更准确地讲，类是在其任何static成员被访问时加载的\)。

#### 示例

**基类**

```text
public class Insect {
    private int i = 9;
    protected int j;
    Insect(){
        System.out.println("i = " + i + ", j = "+j);
        j = 39;
    }
    private static int x1 = printInit("static Insect.x1 initialized");

    static int printInit(String s){
        System.out.println(s);
        return 47;
    }
}
```

**导出类**

```text
public class Beetle extends Insect{
    private int k = printInit("Beetle.k initialized");

     public Beetle(){
         System.out.println("k = "+ k);
         System.out.println("j = "+ j);
     }

     private static int x2 = printInit("static Beetle.x2 initialized");

     public static void main(String[] args){
         System.out.println("Beetle constructor");
         Beetle b = new Beetle();
     }
}
```

**输出结果**

```text
static Insect.x1 initialized
static Beetle.x2 initialized
Beetle constructor
i = 9, j = 0
Beetle.k initialized
k = 47
j = 39
```

**类加载顺序**

在Beetle上运行Java时，所发生的第一件事情就是试图访问Beetle.main\(\)\(一个static方法\)，于是加载器开始启动并找出Beetle类的编译代码\(在名为Beetle.class的文件之中\)。在对它进行加载的过程中，编译器注意到它有一个基类\(这是由关键字extends得知的\)，于是它继续进行加载。不管你是否打算产生一个该基类的对象，这都要发生。

如果该基类还有其自身的基类，那么第二个基类就会被加载，如此类推。接下来，根基类中的static初始化\(在本示例中是Insect\)即会被执行，然后是下一个导出类，以此类推。这种方式很重要，因为导出类的static初始化可能会依赖于基类成员能否被正确初始化。

至此为止，必要的类都已加载完毕，对象就可以被创建了。

首先，对象中所有的基本类型都会被设为默认值，对象引用被设为null——这是通过将对象内存设为二进制零值而一举生成的。

然后，基类的构造器会被调用。基类构造器和导出类的构造器一样，以相同的顺序来经历相同的过程。在基类构造器完成之后，实例变量按其次序被初始化。最后，构造器的其余部分被执行。

