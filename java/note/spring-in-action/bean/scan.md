# 组件扫描与自动装配

#### Spring配置的可选方案

Spring提供了三种主要的装配机制

* 在XML中进行显式配置
* 在Java中进行显式配置
* 隐式的bean发现机制和自动装配

#### 自动化装配bean

Spring从两个角度来实现自动化装配

* 组件扫描
  * Spring会自动发现应用上下文中创建的bean
* 自动装配
  * Spring自动满足bean之间的依赖

#### 组件扫描

组件扫描默认是不启用的，我们还需要显式配置一下Spring，从而命令它去寻找带有@Component注解的类，并为其创建bean。

```text
@Configuration
@ComponentSacn
public class CDPlayerConfig(){}
```

如果没有其他配置的话，@ComponentScan默认会扫描与配置类相同的包。也可以通过value或者basePackages属性指明扫描不同的基础包。

```text
//value
@ComponentScan("soudsystem")
//basePackages
@ComponentScan(basePackages={"soudsystem","video"})
```

除了将包设置为简单的String类型之外，@ComponentScan还提供了另外一种方法，将其指定为包中所包含的类或接口，这些类所在的包将会作为组件扫描的基础包。

```text
@ComponentScan(basePackageClasses={CDPlayer.class,DVDPlayer.class})
```

**为组件扫描的bean命名**

Spring应用上下文中所有的bean都会给定一个ID。自动扫描时，Spring会根据类名为其指定一个ID，也就是将类名的第一个字母变为小写。如果想为这个bean设置不同的ID，所要做的就是将期望的ID作为值传递给@Component注解。

```text
@Component("lonelyHeartsClub")
public class SgtPeppers{}
```

#### 自动装配

自动装备就是让Spring自动满足bean依赖的一种方法，在满足依赖的过程中，会在Spring应用上下文中寻找匹配某个bean需求的其他bean。为了声明要进行自动装配，我们可以借助Spring的@Autowired注解。

```text
@Component
public class CDPlayer {
    @Autowired
    public CDPlayer(CompactDisc cd){
        this.cd =cd;
    }
}
```

```text
@Autowired
public void setCompactDisc(CompactDisc cd){
    this.cd =cd;
}
```

setter方法并没有什么特殊之处，@Autowired注解可以用在类的任何方法上。

假如有且只有一个bean匹配依赖需求的话，那么这个bean将会被装配进来。

如果没有匹配的bean，那么在应用上下文创建的时候，Spring会抛出一个异常。为了避免异常，可以将@Autowired的required属性设置为false;

如果有多个bean都能满足依赖关系的话，Spring将会抛出一个异常，表明没有明确指定要选择哪个bean进行自动装配。

