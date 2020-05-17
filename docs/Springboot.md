# Springboot

[toc]



## dependencies 和dependencyManagement的区别

* dependencies 子moudle 会继承父的
* dependecyManagement 只会继承版本号

## 日期配置

```properties
//
date: 2019-12-01
  

spring:
	jackson:
 		data-format: yyyy-MM-dd
```

## 属性注入

```java
@ConfigurationProperties("prefix = "student")
@value
                         
@Value("#{${a}+${b}}")
                         
                         
@PropertySource(value = {"classpath:person.properties"})
                         
```



## 加载bean.xml配置文件

```
@ImportResource(locations = {"classpath:beans.xml"})
```



## 配置文件优先级

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdx64kjp9qj30ki07ntb6.jpg)

```
1.命令行参数
所有的配置都可以在命令行上进行指定
java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --server.port=8087 --server.context-path=/abc 多个配置用空格分开; --配置项=值
2.来自java:comp/env的JNDI属性 3.Java系统属性(System.getProperties()) 4.操作系统环境变量 5.RandomValuePropertySource配置的random.*属性值
由jar包外向jar包内进行寻找;
优先加载带profile 
6.jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件 
7.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件
再来加载不带profile 
8.jar包外部的application.properties或application.yml(不带spring.profile)配置文件 
9.jar包内部的application.properties或application.yml(不带spring.profile)配置文件
10.@Configuration注解类上的@PropertySource 
11.通过SpringApplication.setDefaultProperties指定的默认属性
```

## Conditional的子类

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdx790nc5pj30vi0kp4ci.jpg)



## springboot经典面试题

### 如何实现自动装配的？

[尚硅谷讲解视频](https://www.bilibili.com/video/BV1Et411Y7tQ?p=18)

### springboot 1.x和2.x的特性不同

>
>
>

## [springboot 源码解析文章汇总](https://mp.weixin.qq.com/s/xlBQTzbCPBuzo0nugUYvYA)

### [SpringBoot自动装配原理解析](https://mp.weixin.qq.com/s?__biz=MzU5MDgzOTYzMw==&mid=2247484694&idx=1&sn=e8d18bf3f2ee144ecb63f1b3d3190033&scene=21#wechat_redirect)

### [ 创建SpringApplication对象实例](https://mp.weixin.qq.com/s?__biz=MzU5MDgzOTYzMw==&mid=2247484695&idx=1&sn=77b4a9e294a559236ea7d81c4ea6e582&scene=21#wechat_redirect)

### [SpringApplication到底run了什么？上](https://mp.weixin.qq.com/s?__biz=MzU5MDgzOTYzMw==&mid=2247484697&idx=1&sn=9b8ed82e7356e7851e7cc3d0ae2c3011&scene=21#wechat_redirect)

### [SpringApplication到底run了什么？下](https://mp.weixin.qq.com/s?__biz=MzU5MDgzOTYzMw==&mid=2247484698&idx=1&sn=a0b0f26148374165fa074606db9927ab&scene=21#wechat_redirect)

### [SpringBoot嵌入式Tomcat的自动配置原理](https://mp.weixin.qq.com/s?__biz=MzU5MDgzOTYzMw==&mid=2247484699&idx=1&sn=714641f9798f53c8e76e2ba987dc3fd9&scene=21#wechat_redirect)

### [Springboot健康检查实现原理](https://mp.weixin.qq.com/s?__biz=MzU5MDgzOTYzMw==&mid=2247484705&idx=1&sn=60443f44a437a210d0b5a5bd4666c7c1&scene=21#wechat_redirect)



### 