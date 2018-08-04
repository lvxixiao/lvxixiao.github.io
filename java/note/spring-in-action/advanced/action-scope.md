# bean的作用域

#### 作用域

在默认情况下，Spring应用上下文中所有bean都是作为单例\(singleton\)的形式创建的。也就是说，不管给定的一个bean被注入到其他bean多少次，每次所注入的都是同一个实例。

在大多数情况下，单例bean是很理想的方案。初始化和垃圾回收对象实例所带来的成本只留给一些小规模任务，在这些任务中，让对象保持无状态并且在应用中反复重用这些对象可能并不合理。

Spring定义了多种作用域，可以基于这些作用域创建bean，包括

* 单例\(Singleton\)
  * 在整个应用中，只创建bean的一个实例
* 原型\(Prototype\)
  * 每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例
* 会话\(Session\)
  * 在Web应用中，为每个会话创建一个bean实例
* 请求\(Request\)
  * 在Web应用中，为每个请求创建一个bean实例

单例是默认的作用域，如果选择其他的作用域，要使用@Scope注解，它可以与@Component或@Bean一起使用。

```text
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Notepad notepad() {
    return new Notepad();
}
```

#### 使用会话和请求作用域

在Web应用中，如果能够实例化在会话或请求范围内共享的bean，那将是非常有价值的事情。

```text
@Component
@Scope(
    value = WebApplicationContext.SCOPE_SESSION,
    proxyMode=ScopedProxyMode.INTEFACES)
public Shopping Cart cart(){...}
```

我们将value设置成了WebApplicationContext中的SCOPE\_SESSION常量\(它的值是session\)。这会告诉Spring为Web应用中的每个会话创建一个实例，在当前会话相关的操作中，这个bean实际上相当于单例。

假设我们要将ShoppingCart bean注入到单例 StoreService bean的Setter方法中

```text
@Component
public class StoreService{
    @Autowired
    public void setShoppingCart(ShoppingCart shoppingCart){
        this.shoppingCart = shoppingCart;
    }
}
```

因为StoreService是一个单例的bean，会在Spring应用上下文加载的时候创建。当它创建的时候，Spring会试图将ShoppingCart bean注入到setShoppingCart\(\)方法中。但是ShoppingCart bean是会话作用域的，此时并不存在。直到某个用户进入系统，创建了会话之后，才会出现ShoppingCart实例。

Spring并不会将实际的ShoppingCart bean注入到StoreService中，Spring会注入到一个ShoppingCart bean的代理。这个代理会暴露与ShoppingCart相同的方法，所以StoreService会认为它就是一个购物车。但是，当StoreService调用ShoppingCart的方法时，代理会对其进行懒解析并将调用委托给会话作用域内真正的ShoppingCart bean。

proxyMode属性被设置成了ScopedProxyMode.INTERFACES，这表明这个代理要实现ShoppingCart接口，并将调用委托给实现bean。

如果ShoppingCart是接口而不是类，这是可以的。但如果ShoppingCart是一个具体的类的话，Spring就没有办法创建基于接口的代理了。此时，它必须使用CGLib来生成基于类的代理。所以，如果bean类型是具体类的话，我们必须要将proxyMode属性设置为ScopedProxyMode.TARGET\_CLASS，以此来表明要以生成目标类扩展的方式创建代理。

尽管以上主要关注了会话作用域，但是请求作用域的bean会面临相同的装配问题。因此，请求作用域的bean应该也以作用域代理的方式进行注入。

