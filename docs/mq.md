#   MQ

[toc]



## rabbitmq

### exchange 种类

* 直接
* topic
* Fanout  不需要匹配topickey





![](https://tva1.sinaimg.cn/large/00831rSTly1gd1xozl5fqj30gy061774.jpg)

### 删除用户

 rabbitmqctl delete zhangsan

Rabbitmqctl list_users

Rabbitmqctl add_user zhangsan

rabbitmqctl set_user_tags zhangsan administrator

###  [确保消息可靠性有两种模式 transaction和confirm模式](https://github.com/shishan100/Java-Interview-Advanced/blob/master/docs/high-concurrency/how-to-ensure-the-reliable-transmission-of-messages.md)



![](https://tva1.sinaimg.cn/large/00831rSTly1gda01mong3j311y0g4n3p.jpg)

![](https://tva1.sinaimg.cn/large/00831rSTly1gda02nqu4yj31010hp44q.jpg)

![](https://tva1.sinaimg.cn/large/00831rSTly1gda06xo43cj30hd07at96.jpg)

### 消息的持久化

* 队列持久化

* 交换机持久化

* 消息持久化

  ![](https://tva1.sinaimg.cn/large/00831rSTly1gda0dj3b2nj30za0c4n72.jpg)

  

  ### 消费者的确认

  自动应答变成手动应答

  ![](https://tva1.sinaimg.cn/large/00831rSTly1gda0k6nypbj30jb0ajwgv.jpg)

  

###  消费者回调

* 消息落库
  * 提供一个回调的API
  * 消费者可以发送消息



### 补偿机制

 ATM 存取款，如果核心没有响应，那么再次发送。

 存-> 发送5次确认  取->发送5次冲正

### 消息幂等性

 broker做不到消息幂等

 重账控制表

### [消息顺序性](https://github.com/shishan100/Java-Interview-Advanced/blob/master/docs/high-concurrency/how-to-ensure-the-order-of-messages.md)

 服务端实现不了 顺序性。

 一个队列只有一个消费者的情况下，才能保证顺序。

 

### 高可用架构

网络敏感，不适合在广域网

* 集群

  * Erlang.cookie hosts
  * 磁盘类型 ： 磁盘和内存
  * 配置步骤： join cluster

* 镜像队列

* HA方案

  ![](https://tva1.sinaimg.cn/large/00831rSTly1gdalyv9gatj315q0legvi.jpg)

### 实践经验总结

* 配置文件与命名规范
* 调用封装
* 信息落库（可追溯，可重发）+定时任务

​         效率降低，占用磁盘空间      

* 如何减少连接数

  划分数据成数组

* 生产者先发送消息还是先登记业务表

   先登记业务表 然后发送消息

  两个系统，第一个数据库回滚了，数据就不一致了

* 谁来创建对象（交换机、队列、绑定关系）

  由消费者创建，谁去使用，谁去创建。

* 运维监控

  Zabbix

* 其他插件

   sharding插件  可以实现分区



### 面试题目

![](https://tva1.sinaimg.cn/large/00831rSTly1gdan7co7x5j30q50iajyh.jpg)



1.

![](https://tva1.sinaimg.cn/large/00831rSTly1gdan8l5l0kj30p306tjx0.jpg)

2. 轮询、

   公平分发。消费者的channel设置 basicQOS(2) ，2个未确定 server就不会给它派发了

   

3. ![](https://tva1.sinaimg.cn/large/00831rSTly1gdanfvhj47j30gy04tjsr.jpg)

   很少出现路由找不到的情况

4.死信是什么？

  队列中的消息到达了死信交换机就叫死信。

 （1）消息被消费者拒绝 单条拒绝 批量拒绝，重新入队为false

   (2)  消息过期，队列属性有过期时间设置，时间到了还没有被消费就去死信队列了 ；消息有个过期时间设置。这       两种

（3）消息堆积。堆积数量超过了队列长度，先入队的变成死信

5. 延迟队列

   过期时间 +死信队列

   有插件

6. 保证消息的可靠性投递

   

7. 消息的幂等，依赖唯一id

8. 流量控制 ： 服务端的流控通过内存（40%）和磁盘（少于1g）来控制

   ​                     消费端 prefetch count

   ​                     网关或接入层

   9.如何保证消息顺序消费

10.集群模式和集群节点类型？

   普通模式 ：

   镜像模式：不同节点 队列同步

磁盘节点（内存+硬盘）至少一个磁盘节点

内存节点





## Kafka

### topic



​    