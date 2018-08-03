# 构造器和多态

#### 构造器的调用顺序\(P159\)

基类的构造器总是在导出类的构造过程中被调用，而且按照继承层次逐渐向上链接，以使每个基类的构造器都能得到调用。这样做是有意义的，因为构造器具有一项特殊任务：检查对象是否被正确地构造。导出类只能访问它自己的成员，不能访问基类中的成员\(基类成员通常是private类型\)。只有基类的构造器才具有恰当的知识和权限来对自己的元素进行初始化。因此，必须命令所有构造器都得到调用，否则就不可能正确构造完整对象。这正是编译器为什么要强制每个导出类部分都必须调用构造器的原因。

#### 构造器内部的多态方法的行为\(P162-163\)

如果要调用构造器内部的一个动态绑定方法，就要用到那个方法的被覆盖后的定义。然而，这个调用的效果可能相当难于预料，因为被覆盖的方法在对象被完全构造之前就会被调用。这可能会造成一些难于发现的隐藏错误。

**例子**

基类

```text
public class Glyph {
    void draw(){
        System.out.println("Glyph draw");
    }
    Glyph(){
        System.out.println("Glyph() before draw()");
        draw();
        System.out.println("Glyph() after draw()");
    }
}
```

导出类

```text
public class RoundGlyph extends Glyph{
    private int radius = 1;
    RoundGlyph(int r){
        radius = r;
        System.out.println("RoundGlyph.RoundGlyph() , radius = " + radius);
    }

    void draw(){
        System.out.println("RoundGlyph.draw() , radius = "+ radius);
    }
}
```

main函数

```text
public static void main(String[] args){
    new RoundGlyph(5);
}
```

输出

```text
Glyph() before draw()
RoundGlyph.draw() , radius = 0
Glyph() after draw()
RoundGlyph.RoundGlyph() , radius = 5
```

Glyph.draw\(\)方法设计为将要被覆盖，这种覆盖是在RoundGlyph中发生的。但是Glyph构造器会调用这个方法，结果导致了对RoundGlyph.draw\(\)的调用。然而，当Glyph的构造器调用draw\(\)方法时，radius不是默认初始值1，而是0。

> 如果radius添加了static关键字，那么当Glyph的构造器调用draw\(\)方法时，radius将为默认值1。因为类加载是在调用构造器之前发生的。

初始化的过程\(不包含类加载\)

1. 在其他任何事物发生之前，将分配给对象的存储空间初始化成二进制的零。
2. 调用基类构造器。此时，被覆盖后的draw\(\)方法\(要在调用RoundGlyph构造器之前调用\)，由于步骤1的缘故，我们此时会发现radius的值为0。
3. 按照声明的顺序调用成员的初始化方法。
4. 调用导出类的构造器主体。

#### 总结\(P164\)

编写构造器时有一条有效的准则：“用尽可能简单的方法使对象进入正常状态；如果可以的话，避免调用其他方法”。在构造器内唯一能够安全调用的那些方法是基类中前期绑定的方法\(final、static\)。这些方法不能被覆盖，因此也就不会出现上述问题。

