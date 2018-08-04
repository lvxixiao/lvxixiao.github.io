# profile

#### 配置profile bean

Spring为环境相关的bean所提供的解决方案其实与构建时的方案没有太大的差别。当然，在这个过程中需要根据环境决定该创建哪个bean和不创建哪个bean。不过Spring并不是在构建的时候做出这样的决策，而是等到运行时再来确定。

Spring引入了bean profile的功能，要使用profile，你首先要将所有不同的bean定义整理到一个或多个profile之中，在将应用部署到每个环境时，要确保对应的peofile处于激活\(active\)的状态。

在Java配置中，可以使用@Profile注解指定某个bean属于哪一个profile。

只有当规定的profile激活时，相应的bean才会被创建，但是可能会有其他的bean并没有声明在一个给定的profile范围内。没有指定profile的bean始终都会被创建，与激活哪个profile没有关系。

```text
@Configuration
@Profile("dev")
public class DevelopmentProfileConfig(){}
```

```text
@Bean
@Profile("dev")
public DataSource embeddedDataSource(){...}
```

#### 激活profile

Sprign在确定哪个profile处于激活状态时，需要依赖两个独立的属性。

* spring.profiles.active
* spring.profiles.default

如果设置了spring.profiles.active属性的话，那么它的值就会用来确定哪个profile是激活的。如果不设置这个属性的话，那Spring将会查找spring.profiles.default的值。如果这两个值都没有设置的话，那就没有激活的profile，因此只会创建那些没有定义在profile中的bean。

profile使用的都是复数形式，这意味着你可以同时激活多个profile，这可以通过列出多个和profile名称，并以逗号分隔来实现。

有多种方式来设置这两个属性

* 作为DispatcherServlet的初始化参数
* 作为Web应用的上下文参数
* 作为JNDI条目
* 作为环境变量
* 作为JVM的系统属性
* 在集成测试类上，使用@ActiveProfiles注解设置

