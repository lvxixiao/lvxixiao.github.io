# 处理自动装配的歧义性

#### 自动装配歧义性

自动装配时，只有一个bean匹配所需的结果时，自动装配才是有效的。

```text
@Autowired
public void setDessert(Dessert dessert){
    this.dessert = dessert;
}
```

```text
@Component
public class Cake implement Dessert{...}

@Component
public class Cookies implements Dessert{...}

@Component
public class IceCream implements Dessert{...}
```

因为这三个实现均使用了@Component注解，在组件扫描的时候，能够发现它们并将其创建为Spring应用上下文里面的bean。然后，当Spring试图自动装配setDessert\(\)中的Dessert参数时，它并没有唯一、无歧义的可选值。因此Spring抛出NoUniqueBeanDefinitionException。

当确实发生歧义性的时候，Spring提供了多种可选方案来解决这样的问题。你可以将可选bean中的某一个设为首选\(primary\)的bean，或者使用限定符\(qualifier\)来帮助Spring将可选的bean的范围缩小到只有一个bean。

#### @Primary

在Spring中，可以通过@Primary来表达最喜欢的方案。@Primary能够与@Component组合用在组件扫描的bean上，也可以与@Bean组合用在Java配置的bean声明中。发生歧义后，@Primary注解的bean将成为首选bean。

```text
@Component
@Primary
public class IceCream implements Dessert{...}
```

```text
@Bean
@Primary
public Dessert iceCream(){
    return new IceCream();
}
```

但是，当两个首选的bean存在时，依然会发生歧义问题。

#### Qualifier

@Qualifier注解是使用限定符的主要方式。它可以与@Autowired协同使用，在注入的时候指定想要注入进去的是哪个bean。

```text
@Aurowired
@Qulifier("iceCream")
public void setDessert(Dessert dessert){...}
```

@Qulifier注解所设置的参数就是想要注入的bean的ID。所有使用@Component注解声明的类都会创建为bean，并且bean的ID为首字母变为小写的类名。

**创建自定义的限定符**

我们可以为bean设置自己的限定符，而不是依赖于将bean ID作为限定符。所需要做的就是在bean声明上添加@Qualifier注解。

```text
@Component
@Qualifier("cold")
public class IceCream implements Dessert(){...}
```

```text
@Bean
@Qualifier("cold")
public Dessert iceCream(){...}
```

当使用自定义的@Qulifier值时，最佳实践是为bean选择特定性或描述性的术语，而不是使用随意的名字。

**自定义的限定符注解**

面向特性的限定符要比基于bean ID的限定符好一些。但是，如果多个bean都具备相同的特性的话，这种做法也会出现新的问题。此时，我们可以使用更多的限定符来将可选范围限定到只有一个bean。

由于Java不允许同一条目上重复出现相同类型的注解，因此我们可以创建自定义的限定符注解。

```text
@Target({ElementType.CONSTRUCTOR,ElementType.FIELD,
        ElementType.METHOD,ElementType.TYPe})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Cold(){}
```

```text
@Target({ElementType.CONSTRUCTOR,ElementType.FIELD,
        ElementType.METHOD,ElementType.TYPe})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Creamy(){}
```

现在，可以重新看一下IceCream了。

```text
@Component
@Cold
@Creamy
public class IceCream implement Dessert(){...}
```

最终，在注入点，我们使用必要的限定符注解进行任意组合，从而将可选范围缩小到只有一个bean满足需求。

```text
@Autowired
@Cold
@Creamy
public void setDessert(Dessert dessert){...}
```

