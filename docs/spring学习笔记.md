#   Spring学习笔记

[toc]

# 博客资源

### [子路博客](https://blog.csdn.net/java_lyvee/article/details/102499560)

### [鲁班学院](https://gitee.com/archguide/dashboard/projects)

### [循环依赖和三级缓存](https://blog.csdn.net/f641385712/article/details/92801300)

### [AOP](https://shimo.im/docs/Nj0bcFUy3SYyYnbI/)

[spring官方文档](https://github.com/DocsHome/spring-docs/blob/master/pages/core/overview.md)

## 类图

![](https://tva1.sinaimg.cn/large/00831rSTly1gd45fiz3o8j31bw0i5jt9.jpg)





## BeanFactory和applicationContext的本质区别

> 司马老师说 前者是懒加载，后者是非懒加载（可以指定为非懒加载）是前者的扩张。

## 什么是控制反转

> 就是依赖倒置原则，生产汽车的例子

> 核心思想:资源不由使用资源的双方管理，而由不使用资源的第三方管理，这可以带来很多好处。
>
> 第一，资源集中管理，实现资源的可配置和易管理。第二，降低了使用资源双方的依赖程度，也就是我们说的耦合度。

```java
@Bean
@Lazy
@Scope(scopeName = "prototype")
```

@Conditional注解 springboot 大量使用 onmissing onclass

```java
@Bean
@Conditional(value = TulingCondition.class)
public TulingLog tulingLog(){
  return new TulingLog();
}

public class TulingCondition implements Condition{
  public boolean matches(ConditionContext context,AnnotatedTypedMetadata metadata){
    if(context.getBeanFactory().containsBean("tulingAspect")){
      return true;
    }
    return false;
  }
}
```



## 往ioc容器添加组件

* @Bean

* @CompentScan + @Controller + @Service + @ Compent @ Repository

* ac.getBeanFactory().registerSingleton()

* @Import

  ```java
  @Import(value={Person.class,TulingImportSelector.class，ImportBeanDefinitionRegistrar.class})
  
  public class TulingImportSelector implements ImportSelector{
    public String[] selectImports(AnnotationMetadata importingClassMetadata){
      return new String[]{"com.tuling.testimport.compent.Dog"};
    }
  }
  
  public class TulingBeanDefinitionRegister implements ImportBeanDefinitionRegistrar{
    public void registerBeanDefinitions(...){
      RootBeandefinition rootBeanDefinition = new RootBeanDefinition(Cat.class);
    }
  }
  ```

* ImportBeanDefinitionRegister

*  通过实现factoryBean接口来实现注册 组件  ,getObjectType的对象是不受 spring容器管理的。

  ```java
  public class CarFactoryBean implements FactoryBean<Car>{
     public Car getObject(){
       return new Car();
     }
     public Class<?> getObjectType(){
       return Car.class;
     } 
    
    public boolean isSingleton(){
      return true;
    }  
  }
  ```



## Bean的生命周期

版本一

> 1. 推断构造方法
> 2. 实例化一个对象
> 3. 属性注入
> 4. 生命周期回调初始化方法
> 5. aop代理

版本二

* 创建 之前

  * BeanDefinitionRegistryPostProcessor 这个在扫描之前可以向容器中添加beanDefinition

  * 初始化容器
  * 加载BeanFactoryPostProcessor实现类 (BeanDefinitionRegistryPostProcessor子接口，调用在这个之前) 
  * 执行BeanFactoryPostProcessor.postProcessBeanFactory方法（主要是用来自定义修改持有的beandefinition）

* 创建 和 初始化 (postcontruct和init指向同一个方法，只会执行一次)

  * Aware接口相关
  * 这里做注入
  * BeanPostProcessor.postProcessBeforeInitialization 
  * JSR250 @PostConstruct
  * InitializingBean接口 afterPropertiesSet()方法
  * 指定的init方法         
  * BeanPostProcessor.postProcessAfterInitialization
  * AOP代理
  
* 销毁  ( 如果是prototype 模式，那么不受spring容器管理）

  (1) JSR250 @ProDestory

  (2)实现 DisposableBean接口 的destory方法

  (3)指定的desotry方法
  
  

## Bean 和 BeanDefinition的区别

> Bean 根据BeanDefinition生成

## BeanDefinitionRegistryPostProcossor

> 继承自 BeanFactoryPostProcessor
>
> ​	postProcessBeanDefinitionRegistry: beanDefinition 加载之前 ，但是有7个(6+1)框架自己的beanDefinition
>
> ​    postProcessBeanFactory: beanDefinition加载之后

## filter 拦截器 AOP

* filter 是依赖servlet，只能获取url

* 拦截器 url 方法名称 但是不能参数名称
* AOP 都可以

## resources 文件夹下面的template 和static文件夹的加载顺序

> DefaultErrorViewResolver.java 先找     template/error/404没有再找静态资源

* 静态资源包含四个路径
  * /META-INF/resources/
  * /resources/
  * /static/
  * /public/

## autowired resource inject的区别





## 不同环境切换数据源

![](https://tva1.sinaimg.cn/large/00831rSTly1gd5dqo1c0rj30az02wq4i.jpg)

## spring 无参构造器



![](https://tva1.sinaimg.cn/large/00831rSTly1gd5duxocxgj30jx05ngpo.jpg)

## 注入模型 （不是注入的方式）

注入模型：找bean 注入bean,   

仅仅针对 xml方式，很多property 比较麻烦，然后自动注入就可以了。

**注意啦！！！**  可以通过修改beandefinition来进行修改。实现set方法

* no

* bytype  setName      :bytype冲突的话 ，不注入，是null  ,primary和autowired-candidate

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1geb1axerbtj30dg07eq5y.jpg)

* byname

* constuctor-----推断构造方法

**小tip: 如果有多个构造函数，选择参数都有且最多的那个**

> 官网说有两种： construct，和set方法
>
> 如果自动注入的话，有四种注入模型，

```
spring注入方式有两种： 1 通过set方法  2 通过构造函数(如果有多个构造函数会选择参数多的构造方法)

自动装配技术(手动装配)：

@Resource: 默认是通过name来查找注入值，如果不存在就报错

@Autowired 通过类型查找(类型)，然后再通过name

以上两种通过反射，然后设置值

AutowireMode(自动装配模型):在spring中有四种模式分别是: 

1 autowire_no(0)： 默认装配模式， 目前非xml配置都是使用这种方式，然后程序员使用注解手动注入

2 autowire_name: 通过set方法，并且 set方法的名称需要和bean的name一致     byName

3 autowire_type: 通过set方法,并且再根据bean的类型，注入属性，是通过类型配置  byType

4 autowire_construcor: 通过构造器注入
————————————————
版权声明：本文为CSDN博主「坑里水库」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_27409289/article/details/100753656
```

## Spring注入方式有几种

constructor

set方法

## resource 和AutoWired的不同点

反射的set方法

一个Spring提供的，一个jdk提供，

一个先type后name，一个先name后type  不叫 byname 和bytype

解析的后置处理器不同

@AutoWired   AutowiredAnnotationBeanPostProcessor

@Resource     CommonAnnotationBeanPostProcessor



## 完成依赖注入的关键后置处理器

* 如果@autowired 用的是下面的处理器

  InstantiationAwareBeanPostProcessor

  **AutowiredAnnotationBeanPostProcessor** 



![](https://tva1.sinaimg.cn/large/007S8ZIlly1geb2oaepq0j30qq0b9n7p.jpg)

* 如果是byname bytype 走的并不是这段代码，而是下面这段

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1geb2vnubxtj30zo0f5h0f.jpg)

##  SmartLifeCycle

isAutoStartup() 不用调用start方法

Eureka 借用的smartLifeCycle的知识

## BeanFactory 和bean

## 如何把一个对象放到spring 容器中？

```java
(1)ac.getBeanFactory().registersigleton(xxx) //自己产生的，依赖关系还得自己维护，除非先注册，然后refersh() applicationcontext
```



## 什么样的对象初始化的时候不会放到容器中

* dependsOn
* prototype
* lazy
* abstract 

扫描->解析成benifiniton验证--> new 对象 开始对象的生命周期



## BeanDefinition 关系图

![](https://tva1.sinaimg.cn/large/00831rSTly1gdi74fqhg4j32090itq44.jpg)



* rootbeandefinition  ---- 作为父bd出现---真实bd出现---不能作为子bd出现

  设置父bd 会抛异常

* 合并的beandefinition 必须用rootbeanbeandefinition 接收，不能用generic，所以 generic 不能替代root

* Generic 可以作为子 也可以作为父

* 子beandefinition 构造方法必须传父beandefinition的name

  > 我的理解，就是子 bd 必须和父bd合并，变成一个rootbd

## mybatis和spring整合的原理



## [spring bean的初始化过程](https://blog.csdn.net/java_lyvee/article/details/102633067)

>ConfigurationClassPostProcessor



## mybatis-spring 使用了beandefinitionRegistryPostProcessor



## 开天辟地的5个类，this.reader 加载到bd map中的

为什么我这里是6个？

![](https://tva1.sinaimg.cn/large/00831rSTly1gdjv84uo2hj314903n765.jpg)

* internalConfigurationAnnotationProcessor 为什么不直接new? 因为程序员可能提供，带有注解，配置bd，不能直接new。这个唯一实现beandefinitionregistra...

* AutowiredAnnotationProcessor 处理autowired注解

* CommonAnnotationProcessor 处理 resource注解

  

## 配置类不必 加@Configuration注解，bd类型为AnnotationBeanDefinition 

就是解析完生产bd，有个属性value 是full 或者 lite

但是： 不加@configuration 单例有可能不生效,因为 config类不会被代理了。

```java
public class Config{
  @Bean
  public E e(){
    return new E();
  }
  
  @Bean
  public F f(){
    e();
    return new F（）；
  }
}
```



## 判断一个类是不是配置类的核心原理

![](https://tva1.sinaimg.cn/large/00831rSTly1gdk0dl7rdgj30u809xqde.jpg)





## Spring 扫描的scanner 不是构造函数里面的那个

这样配置，自己用的那个scanner就可以过滤掉默认的注解

![](https://tva1.sinaimg.cn/large/00831rSTly1gdk7iesvv9j30xs02vjtz.jpg)



## Spring中的扩展点

* BeanFactoryPostProcessor
* BeanDefinitionRegistryPostProcessor
* ImportBeanDefinitionRegistrar



![](https://tva1.sinaimg.cn/large/007S8ZIlly1gefmos5hg4j30em0b3dld.jpg)

## Refresh（）方法执行的顺序



* invokeBeanFactoryPostProcessors

  ![](https://tva1.sinaimg.cn/large/00831rSTly1gdlfwewtshj30rr077wjo.jpg)

​       2.1 执行的是 并不是new 对象。 

​              **ClassConfigurationPostProcessor 执行的postprocessorbeanfactory 的作用主要是把@configuration的类的bd 的类换成一个代理类。防止 @Bean注解的单例bean多次实例化**。



* registerBeanPostProcessors  

## 代理的使用地方

* AOP
* @Configuration

## 循环引用起作用的前提

> 单例 而且 以set方法 注入，不能多例，不能构造函数注入
>
> @Autowired 是利用了反射知识，field.set(value,targetObject) 是set的变体

![](https://tva1.sinaimg.cn/large/00831rSTly1gdnh801zevj30xa0czn7o.jpg)

设置循环引用不生效

![](https://tva1.sinaimg.cn/large/00831rSTly1gdnlypf7apj310i093adu.jpg)

**！！！三级缓存 让 B里面的A 提前完成了动态代理 ！！！ **

第三个map是beanfactory,里面完成了AOP

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gegvgls6enj30sz07tgmv.jpg)

## BeanPostProccssor

### InstantiationAwareBeanPostProcessor

> 这个后置处理有个方法，返回boolean，如果为false 就是不需要完成依赖注入。所有对象！！！



## 合成方法 合成属性 合成类 synthetic

* 内部类的变量，会提供一个合成方法

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gehv48z51sj30qx0fstin.jpg)

* 内部类如果是私有构造函数，会提供合成类 以 $1结尾

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gehv66iymcj30by04sabj.jpg)

## 推断构造函数

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gele76jpzsj30o607idk2.jpg)

## [refresh](https://blog.csdn.net/const_/article/details/104952661)

* preparerefresh() 

  记录启动时间和 标志位

https://blog.csdn.net/qq_29310729/article/details/106376587