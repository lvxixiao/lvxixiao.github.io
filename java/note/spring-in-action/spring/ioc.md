# IOC 容器

#### 容纳Bean

在基于Spring的应用中，你的应用对象生存于Spring容器中。Spring容器负责创建对象，装配它们，配置它们并管理它们的整个生命周期，从生存到死亡。

容器是Spring框架的核心。Spring容器使用DI管理构成应用的组件，它会创建相互协作的组件之间的关联。

Spring容器并不是只有一个。Spring自带了多个容器实现，可以归为两种不同的类型。bean工厂\(由org.springframework.beans.factory.eanFactory接口定义\)是最简单的容器，提供基本的DI支持。应用上下文\(由org.springframework.context.ApplicationContext接口定义\)基于BeanFactory构建，并提供应用框架级别的服务，例如从属性文件解析文本信息以及发布应用事件给感兴趣的事件监听者。

#### 应用上下文

Spring自带了多种类型的应用上下文。

* AnnotationConfigApplicationContext
  * 从一个或多个基于Java的配置类中加载Spring应用上下文。
* AnnotationConfigWebApplicationContext
  * 从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
* ClassPathXmlApplicationContext
  * 从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
* FileSystemXmlApplicationContext
  * 从文件系统下的一个或多个XML配置文件中加载上下文定义。
* XmlWebApplicationContext
  * 从Web应用下的一个或多个XML配置文件中加载上下文定义。

无论从文件系统中装载应用上下文还是从类路径下装载应用上下文，将bean加载到bean工厂的过程都是相似的。例如:

```text
ApplicationContext context = new ClassPathXmlApplicationContext("knight.xml");
```

应用上下文准备就绪之后，我们就可以调用上下文的getBean\(\)方法从Spring容器中获取Bean。

#### bean的生命周期

bean装载

1. Spring对bean进行实例化
2. Spring将值和bean的引用注入到bean对应的属性中
3. 如果bean实现了BeanNameAware接口，Spring将bean的ID传递给setBeanName\(\)方法
4. 如果bean实现了BeanFactoryAware接口，Spring将调用setBeanFactory\(\)方法，将BeanFactory容器实例传入
5. 如果bean实现了ApplicationContextAware接口，Spring将调用setApplicationContext\(\)方法，将bean所在的应用上下文的引用传入进来
6. 如果bean实现了BeanPostProcessor接口，Spring将调用它们的postProcessBeforeInitialization\(\)方法
7. 如果bean实现了InitializingBean接口，Spring将调用它们的afterPropertiesSet\(\)方法。
8. 如果bean使用了init-method声明了初始化方法，该方法也会被调用
9. 如果bean实现了BeanPostProcessor接口，Spring将调用它们的postProcessAfterInitialization\(\)方法。
10. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁。

容器关闭

1. 如果bean实现了DisposableBean接口，Spring将调用它的destroy\(\)接口方法
2. 如果bean使用了destroy-method声明了销毁方法，该方法也会被调用。

