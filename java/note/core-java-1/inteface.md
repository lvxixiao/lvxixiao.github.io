# 接口

## 静态方法

在Java SE 8 中，允许在接口中增加静态方法。

```text
public interface JDK8Interface {  

    // static修饰符定义静态方法  
    static void staticMethod() {  
        System.out.println("JDK8Interface1接口中的静态方法");  
    }  
}
```

可以直接通过**JDK8Interface.staticMethod\(\)** 调用。

## 默认方法

可以为借口方法提供一个默认实现。必须用default修饰符标记这样一个方法。接口的实现类可以不覆盖默认方法。

```text
public interface Comparable<T>{
    default int compareTo(T other) {
        return 0;
    }
}
```

## 解决默认方法冲突

如果现在一个接口中将一个方法定义为默认方法，然后又在超类或另一个接口中定义了同样的方法，会发生什么情况？ 1. 超类优先。如果超类提供了一个具体方法，同名而且有相同参数类型的默认方法会被忽略。 1. 一个类扩展了一个超类同时实现了一个接口，并从超类和接口继承了相同方法。在这种情况下，只会考虑超类的方法，接口的所有默认方法都会被忽略。 2. 接口冲突。如果一个接口提供了一个默认方法，另一个接口提供了一个同名而且参数类型相同的方法，必须覆盖这个方法来解决冲突。

```text
class Student extends Person implements Named {...}
```

Student从Pserson继承了getName方法，Named接口是否为getName提供了默认实现并不会带来什么区别，这正是“类优先”规则

“类优先”规则可以确保与Java SE 7 的兼容性。如果为一个接口增加默认方法，这对于有这个默认方法之前能正常工作的代码不会有任何影响。

不可以让默认方法重新定义Object类中的某个方法，如toString\(\)或equals\(\)。由于“类优先”原则，这样的方法绝对无法超越Object.toString或Object.equals。

