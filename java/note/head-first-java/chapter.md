# Chapter4.How Objects Behave

## Declearing and initializing instance variables

Instance variables always get a default value.If you don't explicitly assign a value variable,the instance variable still has a value!

| Type | Value |
| :--- | :--- |
| integers | 0 |
| floating points | 0.0 |
| booleans | false |
| references | null |

## The difference between instance and local variables

* Instance variables are declared inside a class but not with a method.
* Local variables are declared within a method.

## Comparing variables\(primitives or references\)

* To copare two primitives ,use the == operator
* You want to know if two reference variables refer to a single obeject on the heap,using the == operator
* You want to know if two object are equal.You need the .equal\(\) method.

## 声明和初始化实例变量

实例变量有一个缺省的值。如果你没有明确指定变量的值，实例变量会保持那个缺省的值

| 类型 | 值 |
| :--- | :--- |
| integers | 0 |
| floating points | 0.0 |
| booleans | false |
| references | null |

## 实例变量和局部变量的区别

* 实例变量被声明在类里而不是方法里
* 局部变量被声明在方法里

  **比较基本变量与引用变量**

* 两个基本变量的比较试用 == 操作符
* 你想知道两个引用变量是否指向同一个对象，使用 == 操作符
* 你想知道两个对象是否相等，使用 .equal\(\)方法

