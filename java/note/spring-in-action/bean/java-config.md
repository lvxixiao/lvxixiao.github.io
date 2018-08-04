# 通过Java代码装配bean

#### 创建配置类

@Configuration注解表明这个类是一个配置类，该类包含在Spring应用上下文中如何创建bean的细节。

```text
@Configuration
public class CDPlayerConfig{}
```

#### 声明简单的bean

@Bean注解会告诉Spring这个方法返回一个对象，该对象要注册为Spring应用上下文中的bean。方法体中包含了最终产生bean实例的逻辑。

```text
@Bean
public CompactDisc sgtPeppers(){
    return new SgtPeppers();
}
```

默认情况下，bean的ID与带有@Bean注解的方法名是一样的。如果想为其设置一个不同的名字的话，那么可以重命名该方法，也可以通过name属性指定一个不同的名字。

```text
@Bean(name = "lonelyHeartsClubBand")
public CompactDisc sgtPeppers(){
    return new SgtPeppers();
}
```

#### 借助JavaConfig实现注入

看起来，CompactDisc是通过调用sgtPeppers\(\)得到的，但情况并非完全如此。因为sgtPeppers\(\)方法上添加了@Bean注解，Spring将会拦截所有对它的调用，并确保直接返回该方法所创建的bean，而不是每次都对其进行实际的调用。

默认情况下，Spring中的bean都是单例的。

```text
@Bean
public CompactDisc sgtPeppers(){
    return new SgtPeppers();
}

@Bean
public CDPlyaer cdPlyaer(){
    return new CDPlayer(sgtPeppers());
}
```

还有一种引用bean的方式。

```text
@Bean
public CompactDisc sgtPeppers(){
    return new SgtPeppers();
}

@Bean
public CDPlayer cdPlayer(CompactDisc compactDisc) {
    return new CDPlayer(compactDisc);
}
```

当Spring调用cdPlayer\(\)创建CDPlayer bean的时候，它会自动装配一个CompactDisc到配置方法之中。

通过这种方式引用其他的bean通常是最佳的选择，因为它不会要求将CompactDisc声明到同一个配置类之中。带有@Bean注解的方法可以采用任何必要的Java功能来产生bean实例。

