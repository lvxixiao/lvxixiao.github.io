# Chapter3.Know Your Variables

## variables types

* Variables come in two flavors
  * primitive 
    * as object state\(instance variables\)
  * reference
    * as local variables\( variables decleard within a method \)

## primitive types

| Type | Bit Depth | Value Range |
| :--- | :--- | :--- |
| boolean | JVM-specific | true of false |
| char | 16 bits | 0 to 65535 |
| byte | 8 bits | -128 to 127 |
| short | 16 bits | -32768 to 32767 |
| int | 32 bits | -2147483648 to 2147483647 |
| long | 64 bits | -huge to huge |
| float | 32 bits | varies |
| double | 64 bits | varies |

## controlling your object

* There is actually no such thing as an object variable.
* There's only an object reference variable.
* An object reference variable holds bits that represent a way to access an object.
* It doesn't hold the object itself,but it holds something like a pointer.Or an address.

You can't stuff an object into a variable.Objcet live in one place and one place only -- the garbage collectible heap!

When obeject is not assigned,it is abandoned and eligible for Garbage Collection\(GC\).

## 变量的类型

* 变量有两种类型
  * 基本类型
    * 作为对象里的声明\(实例变量\)
  * 引用类型
    * 作为局部变量\(变量声明在方法内\)

      **基本类型**

      | 类型 | 位深 | 取值范围 |
      | :--- | :--- | :--- |
      | boolean | JVM-specific | true of false |
      | char | 16 bits | 0 to 65535 |
      | byte | 8 bits | -128 to 127 |
      | short | 16 bits | -32768 to 32767 |
      | int | 32 bits | -2147483648 to 2147483647 |
      | long | 64 bits | -很多到+很多 |
      | float | 32 bits | 变化的 |
      | double | 64 bits | 变化的 |

      **控制你的对象**
* 实际上没有对象变量
* 仅仅只是对象引用变量
* 对象引用变量是二进制数，代表进入对象的方法
* 对象引用变量不是对象，但它像指针或者地址。

### 你不能把对象塞进变量。对象只存在一个地方--垃圾回收堆\(GC\)!

当对象没有被变量指定，它将会被丢弃，并符合垃圾收集\(GC\)的条件。

