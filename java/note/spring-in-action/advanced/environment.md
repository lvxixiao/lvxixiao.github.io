# Environment

#### 注入外部的值

在Spring中，处理外部值的最简单方式就是声明属性源并通过Spring的Environment来检索属性。

```text
@Configuration
//声明属性源
@PropertySource("classpath:/com/soundsystem/app.prooerties")
public class ExpressiveConfig(){
    @Autowired
    Environment env;

    @Bean
    public BlankDisc disc(){
        //检索属性值
        return new BlankDisc(
            env.getProperty("disc.title")
        );
    }
}
```

在本例中，@PropertySource引用了类路劲中一个名为app.properties的文件。

```text
//app.properties
disc.title=title
```

这个属性文件会加载到Spring的Environment中，稍后可以从这里检索属性。

#### Environment

Environment提供了获取属性值的一些方法

* Environment
  * String getProperty\(String key\)
  * String getProperty\(String key,String defaultValue\)
  * T getProperty\(String key, Class type\)
  * T getProperty\(String key, Class type, T defaultValue\)
  * String getRequiredProperty\(String key\)
    * 查询的属性必须有定义，否则抛出将会抛出IllegalStateException异常。
  * boolean containsProperty\(String key\)
    * 检查属性是否存在
  * Class getPropertyAsClass\(String key, T.class\)
  * String\[\] getActiveProfiles\(\)
  * String\[\] getDefaultProfiles\(\)
  * boolean acceptsProfiles\(String... profiles\)
    * 如果environment支持给定profile的话，就返回true。

Environment还提供了几个与属性相关的方法，如果你在使用getProperty\(\)方法的时候没有指定默认值，并且这个属性没有定义的话，获取到的值是null。如果你希望这个属性必须要定义，那么可以使用getRequiredProperty\(\)方法。

如果使用getRequiredProperty\(\)方法查询的属性没有定义的话，将会抛出IllegalStateException异常。

```text
@Bean
public BlankDisc disc(){
    return new BlankDisc(
        env.getRequiredProperty("disc.title");
    );
}
```

