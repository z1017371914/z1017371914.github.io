## Flush

1.**为什么有时候很快的一条SQL，突然会很慢**？

- 因为进行了Flush，将内存中的数据持久化到磁盘中。

InnoDB使用的是redo log来更新数据，当一定时间或者事件触发时就会将内存中的`脏页`持久化到磁盘中，从而能变成`干净页`

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gtfdmupu5bj60kw0n3doo02.jpg)

2.`何时触发Flush`：

- **redo log已经写满**：write pointer和check pointer相遇，得让check pointer移动，就得将redo log中的改动持久化到磁盘中

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gtfdobh2fsj60ho0ez0us02.jpg)

- **内存空间不足**，将脏页持久化到磁盘中

- **MySQL空闲时**，闲着也是没事干，就持久化到磁盘中
- **MySQL正常关闭时**

