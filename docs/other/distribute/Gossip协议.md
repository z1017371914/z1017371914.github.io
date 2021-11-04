## Gossip协议

1.`出现原因`两阶段提交和Raft算法，都需要大多数节点同意才能进行。如果是类似于告警系统的场景，需要尽管只有一个节点也能对外服务，这时候就需要`Gossip协议`

2.`Gossip协议`：类似于流言蜚语一样，利用随机，具有传染性的方式，将信息散布到整个集群中，一定时间后实现节点数据一致。

3.`Gossip三大重要特点`：**Direct Mail**，**Anti-entropy**，**Rumor mongering**

-------

### Direct Mail

1.`直接邮寄`：直接将信息发送给某个节点，如果数据发送失败，就将信息缓存在自己的缓存队列中，然后重传。

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gtblbho6wuj30on0g0wfv.jpg)

2.`问题`：有可能会因为缓存队列消息过多，导致消息丢失，无法实现`最终一致性`

-----

### Anti-entropy

1.`反熵`：集群中的节点，定时的随机选择集群中的节点进行信息交换，来使得二者数据进行一致。最后达成`最终一致性`. 

反熵很好理解，就是通过不断你的交换，减少混乱程度，也是就朝着熵减的方向进行，所以叫做反熵。

2.`反熵的三种方式`：**推**，**拉**，**推拉**

假设初始状态如下：

![image-20210810124836343](https://tva2.sinaimg.cn/large/0085EwgIgy1gtf17yvdoqj60o407ydit02.jpg)

3.`推`：将自己的消息推送给对方。

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1gtbljmt43ij30ov08641h.jpg)

4.`拉`：拉取对方的信息给自己

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gtblk4xgfdj30nz07ttbw.jpg)

5.`推拉`：双方节点互换信息达成完全一致。

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gtbll1ft3pj30o506n0vv.jpg)

6.`反熵的缺点`：不适合过多的节点；要求节点都是已知的。

如果在检测节点是否存活的场景，是不适用的。

而`谣言传播`可以实现。

----

### Rumor mongering

1.`谣言传播`：节点存活时，周期性的发送消息给其他节点。其他节点也会发送给更多节点，消息就像谣言一样散播，实现最终一致性。

