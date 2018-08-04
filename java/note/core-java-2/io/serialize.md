# 对象输入、输出流与序列化

## 保存和加载序列化对象（P66-67）

为了保存对象数据，首先需要打开一个ObjectOutputStream对象：

```text
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("employee.dat"));
```

现在，可以直接使用ObjectOutputStream的writeObject方法

```text
Employee harry = new Employee("Harry Hacker",50000,1989,10,1);
out.writeObject(harry);
```

为了将这些对象读回，首先需要获得一个ObjectInputStream对象：

```text
ObjectInputStream in = new ObjectInputStream(new FileInputStream("employee.dat"));
```

然后，用readObject方法以这些对象被写出时的顺序获得它们：

```text
Employee e1 = (Employee) in.readObject();
```

**对于希望在对象输出流中存储或从对象输入流中恢复的所有类都应该实现Serializable接口。**

序列化的另一种非常重要的应用是通过网络将对象集合传送到另一台计算机上，正如在文件中保存原生的内存地址毫无意义逸雅阁，这些地址对于在不同的处理器之间的通信也是毫无意义的。因为序列化用序列号代替了内存地址，所以它允许将对象集合从一台机器传送到另一台机器。

## 序列化技术（P67）

每个对象都是用一个序列号\(serial number\)保存的，这就是这种机制之所以被称为对象序列化的原因

* 对你遇到的每一个对象引用都关联一个序列号
* 对于每个对象，当第一次遇到时，保存其对象数据到输出流中
* 如果某个对象之前已经被保存过，那么只写出“与之前保存过的序列号为x的对象相同”

在读回对象时，整个过程是反过来的

* 对于对象输入流中的对象，在第一个遇到其序列号时，构建它，并使用流中数据来初始化它，然后记录这个顺序号和新对象之间的关联。
* 当遇到“与之前保存过的序列号为x的对象相同”标记时，获取与这个顺序号相关联的对象引用。

## API（P70）

* java.io.ObjectOutputStream 1.1
  * ObjectOutputStream\(OutputStream out\)
    * 创建一个ObjectOutputSteram，使得你可以将对象写出到指定的OutputStream。
  * void writeObject\(Object obj\)
    * 写出指定的对象到ObjectOutputStream，这个方法将存储指定对象的类、类的签名以及这个类及其超类中所有非静态和非瞬时的域的值。
* java.io.ObjectInputStream 1.1
  * ObjectInputStream\(InputStream in\)
    * 创建一个ObjectInputStream用于从指定的InputStream中读回对象信息。
  * Object readObject\(\)
    * 从ObjectInputStream中读入一个对象。特别是，这个方法会读回对象的类、类的签名以及这个类及其超类中所有非静态和非瞬时的域的值。它执行的反序列化允许恢复多个对象引用。

