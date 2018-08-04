# 数字签名

#### 消息摘要\(P445-447\)

消息摘要\(message digest\)是数据块的数字指纹。例如，所谓的SHA1\(安全散列算法\)可将任何数据块，无论其数据有多长，都压缩为160位\(20字节\)的序列。

消息摘要具有两个基本属性

* 如果数据的1位或者几位改变了，那么消息摘要也将改变
* 拥有给定消息的伪造者不能创建与原消息具有相同摘要的假消息

此外消息摘要算法还有**SHA-256**、**SHA-384**和**SHA-S12**。

**java创建消息摘要**

MessageDigest类是用于创建封装了指纹算法的对象的“工厂”，它的静态方法getInstance返回继承了MessageDigest类的某个类的对象。这意味着MessageDigest类能够承担下面的双重职责：

* 作为一个工厂类
* 作为所有消息摘要算法的超类

```text
//获取一个能计算SHA指纹的对象的方法
MessageDigest alg = MessageDigest.getInstance("SHA-1");
```

在获取MessageDigest对象之后，可以通过反复调用update方法，将信息中的所有字节提供给该对象。

```text
InputStream in = ...
int ch;
while((ch = in.read()) != -1)
    alg.update((byte) ch);
```

或者

```text
byte[] bytes = ...;
alg.update(bytes);
```

当完成上述操作后，调用digest方法。该方法按照指纹算法的要求补齐输入，并且进行相应的计算，然后以字节数组的形式返回消息摘要。

```text
byte[] hash = alg.digest();
```

#### 消息签名\(P448\)

公共密钥加密技术是基于公共密钥和私有密钥这两个基本概念的。它的设计思想是你可以将公共密钥告诉世界上的任何人，但是，只有自己才持有私有密钥。

用私有密钥对消息摘要进行恰名，然后用公共密钥对该签名进行校验。

