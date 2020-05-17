# redis

[toc]

## 持久化

* AOF

  Appendfsync always

  appendfsync everysec

  Appendfsync no 

  ![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq3n74sjaj30o101e0um.jpg)

  bgrewriteaof  重写aof文件命令

* RDB

​        save 900 1

* 混合持久化 4.0之后

  Aof-use-rdb-preamble yes  aof文件上半部分是 rdb格式，下半部分是 aof格式

  

## 主从

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq4hiaaroj30ke0brn1b.jpg)

     ### 主从复制原理

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq4qnlv0nj30mf0e4diu.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq4x6m52ij30po0fdtck.jpg)

## 哨兵模式

弊端：master 宕机，重新连接需要几秒钟

​           只有master可以写，从节点只可以读

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq546bwz4j30ul0fsdkx.jpg)

client 连接的是哨兵！！！

哨兵通知客户端 master的ip



![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq580oc6kj30yq0jq131.jpg)

## 集群

不要超过1000个

### redis集群为什么至少需要三个master节点

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdqz0evshnj30xi03cwig.jpg)