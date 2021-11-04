## Quorum NWR算法

1.`Quorum NWR算法`：可灵活地实现CAP中C的等级，也就是一致性的等级。

2.`应用场景`：在一个AP系统中，可能新业务需要你能实现一个功能，使得用户改动能在任意节点都能马上获取到，也就是需要从`最终一致性`变成`强一致性`.当然重构系统虽然可行，但是未免太过困难。而`Quorum NWR`算法可解决这个问题.只要设置`W + R > N`即可实现

3.`Quorum NWR三要素`：**N**，**W**，**R**

| 计算共识    | 实现效果   |
| ----------- | ---------- |
| `W + R > N` | 强一致性   |
| `W + R < N` | 最终一致性 |



---

### 复制数N

1.`复制数N`：集群中某个数据的副本数量。

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gtbm4em25mj30kh0cwtb8.jpg)

例如DATA-1的N为2，DATA-2的N为3，DATA-3的N为1.

**可见复制数并不需要等于节点数。**

### 写一致性级别W

1.`写一致性级别W`：Write Consistency Level

2.`含义`：当完成W个副本的更新后，才算完成写操作。

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gtbm7lzsahj30kl0cfdij.jpg)

例如图中W为2，当对节点A和节点C中的DATA-2进行更新后，就算完成写操作。可问题是，DATA-2有三个节点，节点B没有进行操作，如何实现强一致性呢？这时候需要配合`读一致性级别R`来实现

### 读一致性级别R

1.`读一致性级别R`：Read Consistency Level

2.`含义`：当读取一个数据时，需要读取R个副本。

![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gtbmfn1z2vj30lk0cugor.jpg)

例如此次需要访问DATA-2，访问到了上述说的未进行更新的节点B，由于R = 2，W + R > N，因此必定要访问除了B以外的A或C来取数据。而A和C中的数据都是最新的，就会以此为结果进行返回。

可见，就算出现访问到了未更新的节点，也能够通过R来返回最新的数据。