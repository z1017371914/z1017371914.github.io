### 一致Hash算法 

1.`出现原因`：前文提到，Raft集群读写的任务都集中到了leader节点，集群性能相当于单机系统。

传统解决思路：创建多个集群，加入Proxy层，当有请求到来时，通过Hash映射请求到某个集群。

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gtaywn4ycoj30nq0j1di9.jpg)

缺点：当集群数量变更时，需要进行rehash，涉及到了数据的迁移，代价会很大。

**因此，出现了一致性Hash算法**

2.`普通Hash的痛点`：集群数量的变更导致数据迁移十分频繁。

例如当三个节点时key-01映射到的集群是A

![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gtayzikdqvj30nh0imwhd.jpg)

但是如果集群数增加为4时，key-01映射到的节点变成了B，不再是A，但我们需要的数据是存在A中的。

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gtayzp6vn5j30ny0ectb3.jpg)

为了解决这种情况，普通Hash算法就只能对数据和节点**重新映射**。

3.`一致Hash算法`：

一致性Hash算法不是对节点数量取模，而是对2^32 -1 取模，定位到某个位置然后顺时针往下找，直到找到对应的节点，那就是需要的服务节点。

![image-20210809235624064](https://tvax1.sinaimg.cn/large/0085EwgIgy1gtazp1hq7oj30ni0o9q43.jpg)

![image](https://tva3.sinaimg.cn/large/0085EwgIgy1gtaz7xtr0fj30ne0njq58.jpg)

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gtaz84tlxjj30ov0mkmzw.jpg)

节点发生故障，去掉节点C时：

![image](https://tvax3.sinaimg.cn/large/0085EwgIgy1gtaz8p68e1j30oq0lj0vl.jpg)

新增节点D：只有key-03需要映射到节点D，其他都不变，减少了重新映射的过程

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gtaz9pqudpj30n90m8diz.jpg)

4.一致Hash的`热点服务处理方法`：虚拟节点。

在一致哈希中，如果节点太少，容易因为节点分布不均匀造成数据访问的冷热不均，也就是说大多数访问请求都会集中少量几个节点上：

![image](https://tvax3.sinaimg.cn/large/0085EwgIgy1gtazc8glxgj30nh0mvgop.jpg)

解决方法：添加虚拟节点，使得节点分布均匀，然后虚拟节点映射到实际的节点。

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gtazgm6fqaj30pq0nqae2.jpg)