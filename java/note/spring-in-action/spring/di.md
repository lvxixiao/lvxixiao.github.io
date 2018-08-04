# 依赖注入

#### 依赖注入

任何一个有实际意义的应用都会由两个或者更多的类组成，这些类相互之间进行协作来完成特定的业务逻辑。按照传统的做法，每个对象负责管理与自己相互协作的对象\(即它所依赖的对象\)的引用，这将会导致高度耦合和难以测试的代码。

通过依赖注入，对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候进行设定。对象无需自行创建或管理它们的依赖关系。依赖关系将被自动注入到需要它们的对象当中去。

#### 装配

创建应用组件之间协作的行为通常称为装配\(wiring\)。Spring有多种装配bean的方式，采用XML是很常见的一种装配方式。

```text
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  http://www.springframework.org/schema/context/spring-context.xsd
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.springframework.org/schema/beans>

  <bean id="knight" class="com.springinaction.knights.BraveKnight">
    <constructor-arg ref="quest" />
  </bean>

  <bean id="quest" class="com.springinaction.knights.SlayDragonQuest">
    <constructor-arg valu="#{T(System).out}" />
  </bean>
</beans>
```

Spring还支持使用Java来描述配置

```text
@Configuration
public class KnightConfig {
    @Bean
    public Knight knight() {
        return new BraveKnight(guest());
    }

    @Bean
    public Quest quest() {
        return new SlayDragonQuest(System.out);
    }
}
```

#### 应用上下文

Spring通过应用上下文\(Application Context\)装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。Spring自带了多种应用上下文的实现，它们之间主要的区别仅仅在于如何加载配置。

因为knights.xml中的bean是使用XML文件进行配置的，所以选择ClassPathXmlApplicationContext作为应用上下文相对比较合适的。该类加载位于应用程序类路径下的一个或多个XML配置文件。

```text
public static void main(String[] args) throws Exception {
    ClassPathXmlApplicationContext context = 
        new ClassPathXmlApplicationContext("META-INF/spring/knights.xml");
    Knight knight = context.getBean(Knight.class);
    knight.embarkOnQuest();
    context.close();
}
```

对于基于Java的配置，Spring提供了AnnotationConfigApplicationContext。

