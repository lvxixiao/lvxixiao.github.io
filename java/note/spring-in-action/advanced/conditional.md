# 条件化bean

#### 条件化bean

Spring4引入了一个新的@Conditional注解，它可以用到带有@Bean注解的方法上。如果给定的条件计算结果为true，就会创建这个bean，否则的话，这个bean会被忽略。

```text
@Bean
@Conditional(MagicExistsCondition.class)
public MagicBean magicBean(){
    return new MagicBean();
}
```

可以看到，@Confitional中给定了一个Class，它指明了条件，在上面例子中也就是MagicExistsCondition。@Conditional将会通过Condition接口进行条件对比。

```text
public inteface condition{
    boolean matches(ConditionContext ctxt,
                AnnotatedTypeMetadata metadata);
}
```

设置给@Conditional的类可以是任意实现了Condition接口的类型。如果matches\(\)方法返回true，那么就会创建歹有@Conditional注解的bean。如果matches\(\)方法返回false，将不会创建这些bean。

**ConditionContext**

ConditionContext是一个接口,通过这个接口，我们可以做到如下几点

* 借助getRegistry\(\)返回的BeanDefinitionRegistry检查bean定义
* 借助getBeanFactory\(\)返回的ConfigurableListableBeanFactory检查bean是否存在，甚至探查bean的属性
* 借助getEnvironment\(\)返回的Environment检查环境变量是否存在以及它的值是什么
* 读取并探查getResourceLoader\(\)返回的ResourceLoader所加载的资源
* 借助getClassLoader\(\)返回的ClassLoader加载并检查类是否存在

**AnnotatedTypeMetadata**

AnnotatedTypeMetadata能够让我们检查歹有@Bean注解的方法上还有什么其他注解。

借助isAnnotated\(\)方法，我们能够判断带有@Bean注解的方法是不是还有其他特定的注解。借助其他的那些方法，我们能够检查@Bean注解的方法上其他注解的属性。

