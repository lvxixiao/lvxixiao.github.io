# 方法和调用绑定

#### 绑定

将一个方法调用同一个方法主体关联起来被称作绑定。

#### 前期绑定

若在程序执行前进行绑定\(如果有的话，由编译器和连接程序实现\)，叫做连接绑定。

#### 后期绑定

又称动态绑定或运行时绑定，在运行时根据对象的类型进行绑定。

如果一种语言想实现后期绑定，就必须具有某种机制，以便在运行时能判断对象的类型，从而调用恰当的方法。也就是说，编译器一直不知道对象的类型，但是方法调用机制能找到正确的方法体，并加以调用。后期绑定机制随编程语言的不同而有所不同。

Java中除了static方法和final方法\(private方法属于final方法\)之外，其他所有的方法都是后期绑定。除此之外，域也不是后期绑定。

#### 示例

**基类**

```text
public class Insect {
    public int i = 0;

    public Insect(){
    }
    public void print(){
        System.out.println("Insert");
    }

    public static void printStatic(){
        System.out.println("static:Insert");
    }
}
```

**导出类**

```text
public class Beetle extends Insect{
    public int i = 1;

    public Beetle(){
    }
    public void print(){
        System.out.println("Bettle");
    }

    public static void printStatic(){
        System.out.println("static:Bettle");
    }
}
```

**main函数**

```text
public static void main(String[] args){
        Insect insect = new Insect();
        Insect beetle = new Beetle();
        insect.printStatic();
        beetle.printStatic();
        System.out.println(insect.i);
        System.out.println(beetle.i);
    }
```

**输出**

```text
static:Insert
static:Insert
0
0
```

