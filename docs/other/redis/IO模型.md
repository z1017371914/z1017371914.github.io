## Redis I/O模型

1.`Redis单线程`：网络IO，键值对读写是单线程

2.`Redis多线程`：持久化、异步删除、集群数据同步

3.`单线程快的原因`：IO多路复用

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gtge8lfhcqj60re0hp0wk02.jpg)

