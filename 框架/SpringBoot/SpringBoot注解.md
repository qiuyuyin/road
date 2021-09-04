# SpringBoot注解详解

## 启动类注解

### @SpringBootApplication

主配置类：

```java
@SpringBootApplication
public class MallDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(MallDemoApplication.class, args);
    }

}
```

让我们进入SpringBootApplication

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
)
```

可以得到三个主要的注解结构：

- SpringBootConfiguration

  表示这是一个配置的类，不用使用xml就可以直接对类进行配置

  ```java
  @Target({ElementType.TYPE})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Configuration //表示是一个配置类
  @Indexed
  public @interface SpringBootConfiguration {
      @AliasFor(
          annotation = Configuration.class
      )
      boolean proxyBeanMethods() default true;
  }
  
  ```

- @EnableAutoConfiguration

  ```java
  @Target({ElementType.TYPE})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Inherited
  @AutoConfigurationPackage
  @Import({AutoConfigurationImportSelector.class})
  public @interface EnableAutoConfiguration {
      String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";
  
      Class<?>[] exclude() default {};
  
      String[] excludeName() default {};
  }
  ```

  发现一个比较重要的注解：

  **@AutoConfigurationPackage**

  这个注解比较厉害，可以直接扫描主配置类（即@SpringBootApplication标注的类）的所在包及子包里面所有组件扫描加载到Spring容器。因此我们要把DemoApplication放在项目的最高级中（最外层目录）。

- @ComponentScan 

  这个注解比较常见，就是对一个包进行扫描寻找被@Component注释的类，然后将这个包加入到所需要的类中。

## Spring Bean相关

### 1.@Component

这是一个可以将一个类作为Bean的组件，可以配合ComponentScan来进行自动注入Bean

- @Service 对应的是服务层
- @Controller 对应的是Spring MVC控制层
- @Repository 对应的是Dao层

### 2.@AutoWired

这是一个提升了整个Spring发展的注释，在Spring进入注释时代时就已经存在的一个注释类，可以将一个已经存在的Bean组件注入一个新的组件中，从而减少了人工对引用的操作。

首先来看一下使用xml的形式来进行表示：

```java
//类B
public class ClassB {

    @Data
    private ClassA classA;
    
    
    private String name;
}

//类A
public class ClassA {
    @Data
    private String name = "jenny";
}

//配置文件
<bean id="classB" class="autowired.ClassB">
    <property name="classA" ref="classA"/>
    <property name="name" value="jennyB"/>
</bean>

<bean id="classA" class="autowired.ClassA">
    <property name="name" value="jennyA"/>
</bean>

```

在对一个类包含另一个组件时，还需要对ref进行指定，虽然看起来比较简单，但是在Java Configuration的情况下，就会出现无从下手的情况。

这样就会十分麻烦，这个时候加入一个新的组件便会大幅度提升构建的速度，便出现了@Auto Wired组件。

上面的结构可以通过这样来进行实现：

```java
//类B
@Data
public class ClassB {

    @AutoWired
    private ClassA classA;
    
    private String name;
}

//类A
@Data
public class ClassA {
    private String name = "jenny";
}

//配置文件
<bean id="classB" class="autowired.ClassB">
    
    <property name="name" value="jennyB"/>
</bean>

<bean id="classA" class="autowired.ClassA">
    <property name="name" value="jennyA"/>
</bean>

```

到SpringBoot中已经不需要Java Config和xml文件了，只需要将这两个类加上@Component这个注解即可。

### 3.@RestController

在这个注解中其实就是@Controller和@ResponseBody的合集，默认对一个返回接口返回 Json 类型的格式文件。

使用在RestFul风格的Controller中。

### 4.@Scope

这是一个声明Bean组件作用域的注解：

- singleton 单例模式，就是一个bean生成的对象仅存在一个，同时也是SpringBoot默认的作用域
- prototype 原型模式，每次新的自动注入都会出现一个新的bean实例，***\*springboot的prototype创建的对象不会复制bean的数据内容，属性都为默认值\****
- requestweb请求范围，（最近遇到的问题就是和request作用域的bean有关，才发现之前的理解有偏差），当使用该作用域范围时（包括下面的session作用域），必须指定proxyMode作用域代理类型，否则将会报错，对于request作用域的bean，（之前一直理解的是每次有http请求时都会创建），但实际上并不是这样，而是Spring容器将会创建一个代理用作依赖注入，只有在请求时并且请求的处理中需要调用到它，才会实例化该目标bean。
- sessionweb会话范围，这个和request类似，同样必须指定proxyMode，而且也是Spring容器创建一个代理用作依赖注入，当有会话创建时，并且在会话中请求的处理中需要调用它，才会实例话该目标bean，由于是会话范围，生命依赖于session。

## 前端参数获取

### @PathVariable

通过声明得到一个RestFul风格的参数。

### @RequestBody

通过在方法的参数中声明得到一个前端传递过来的参数。

下面是使用的方式：

```java
@RequestMapping(value = "/update/{id}", method = RequestMethod.POST)
@ResponseBody
public CommonResult updateBrand(@PathVariable("id") Long id, @RequestBody PmsBrand pmsBrandDto, BindingResult result) {
    CommonResult commonResult;
    int count = demoService.updateBrand(id, pmsBrandDto);
    if (count == 1) {
        commonResult = CommonResult.success(pmsBrandDto);
        LOGGER.debug("updateBrand success:{}", pmsBrandDto);
    } else {
        commonResult = CommonResult.failed("操作失败");
        LOGGER.debug("updateBrand failed:{}", pmsBrandDto);
    }
    return commonResult;
}
```

## 配置信息

### @Value

可以通过Application中获取信息来填入value中得到值。

但是在项目架构中并不推荐这种方式来进行构建，而是通过@ConfigurationProperties(prefix="。。。")来进行架构，可以更整洁。

```java
public class A{
	 @Value("${push.start:0}")    如果缺失，默认值为0
     private Long  id;
}
```

### @ConfigurationProperties

通过这种方式来对@Bean中的值进行赋值是最推荐的，Spring会通过扫描字段的名字来添加对应的Value，而不用特定的指定。

最典型的使用方式，提供 prefix 和 locations 两个属性指定属性文件的来源和属性的前缀：

```java
@ConfigurationProperties(prefix = "dolphin", locations = "classpath:dolphin.properties")
public class DolphinServerProperties {
    
}
```

### @EnableConfigurationProperties

@EnableConfigurationProperties(ProfileProperties.class)，通过这种方式可以将一个已经生成的ConfigurationProperties加入到Bean文件中：

下面的例子，通过在@EnableConfigurationProperties注解中直接简单的列出ConnectionSettings.class 来快捷的注册@ConfigurationProperties bean的定义。

因为是在SpringBoot中，所以可以自动的对Configuration进行配置。

```java
@Configuration
@EnableConfigurationProperties(ConnectionSettings.class)
public class MyConfiguration {
}
```

