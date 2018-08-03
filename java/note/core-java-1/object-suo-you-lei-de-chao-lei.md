# Object:所有类的超类

## equals方法（P166）

Object类中的equals方法用于检测一个对象是否等于另一个对象。在Object类中，这个方法将判断两个对象是否具有相同的引用。

## 相等测试与继承（P166-169）

Java语言规范要求equals方法具有下面的特性： 1. 自反性：对于任何非空引用x，x.qeuals\(x\)应该返回true。 2. 对称性：对于任何引用x和y，当且仅当y.equals\(x\)返回true，x.equals\(y\)也应该返回true。 3. 传递性：对于任何引用x、y和z，如果x.equals\(y\)返回true，y.equals\(z\)返回true，x.equals\(z\)也应该返回true。 4. 一致性：如果x和y引用的对象没有发生变化，反复调用x.equals\(y\)应该返回同样的结果。 5. 对于任意非空引用x，x.equals\(null\)应该返回false。

* 如果子类能够拥有自己的相等概念，则对称性需求将强制采用getClass进行检测。
* 如果由超类决定相等的概念，那么就可以使用instanceof进行检测，这样可以在不同子类的对象之间进行相等的比较。

  **编写完美equals方法的建议（P169-170）**

  1. 显式参数命名为otherObject，稍后需要将它转换成另一个叫做other的变量。
  2. 检测this与otherOvject是否引用同一个对象
  3. 检测otherObject是否为null，如果为null，返回false。
  4. 比较this与otherObject是否属于同一个类
     1. 如果equals的语义在每个子类中有所改变，就使用getCalss检测.
        1. if\(getClass != toherObject.getClass\(\)\) return false;\
     2. 如果所有的子类都拥有统一的语义，就使用instanceof检测：
        1. if\(!\(otherObject instanceof ClassName\)\) return false;
  5. 将otherObject转换为相应的类类型变量
     1. ClassName other = \(ClassName\)otherObject
  6. 现在开始对所有需要比较的域进行比较了。使用==比较基本类型域，使用equals比较对象域。

     **String类的equals实现**

     说明。anObject instanceof String表达式中，如果anObject为null，返回false。

     ```text
     public boolean equals(Object anObject) {
     if(this == anObject)
       return true;
     if(anObject instanceof String) {
       String anotherString = (String)anObject;
       int n = value.length;
       if (n == anotherString.value.length) {
           char v1[] = value;
           char v2[] = anotherString.value;
           int i = 0;
           while(n-- != 0) {
               if(v1[i] != v2[i])
                   return false;
               i++;
           }
       }
     }
     return false;
     }
     ```

     **hashCode方法（P170-172）**

     散列码（hash code）是由对象导出的一个整型值。散列码是没有规律的。如果x和y为不同的对象，x.hashCode\(\)和y.hashCode\(\)基本上不会相同。

在Object类中的hashCode\(\)方法返回的值是对象的存储地址。

equals与hashCode的定义必须一致，如果x.equals\(y\)返回true，那么x.hashCode\(\)就必须与y.hashCode\(\)具有相同的值。

