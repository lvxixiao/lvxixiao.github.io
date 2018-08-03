# 对象的创建过程



假设有个名为Dog的类

1. 即使没有显式地使用static关键字，构造器实际上也是静态方法。因此，当首次创建类型为Dog的对象时（构造器可以看成静态方法），或者Dog类的静态方法/静态域首次被访问时，Java解释器必须查找路径，以定位Dog.class文件。
2. 然后载入Dog.class，有关静态初始化的所有动作都会执行。因此，静态初始化只在Class对象首次加载的时候进行一次。
3. 当用new Dog\(\)创建对象的时候，首先将在堆上为Dog对象分配足够的存储空间。
4. 这块存储空间会被清零，这就自动地将Dog对象中的所有基本类型数据都设置成了默认值，而引用被设置成了null。
5. 执行所有出现于字段定义的初始化动作
6. 执行构造器。
