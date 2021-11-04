## count的实现

如果需要统计行数，可以使用

```sql
select count(*) from table
```

但是，纪录增多时，执行这个的时间很慢。

按理来说，不是只要记录总数，读出来就可以了吗？

这就需要了解count(*)的实现方式

### count(*)的实现

InnoDB由于支持事务，实现了MVCC，那么它自己也无法知道究竟有多少行纪录。

- 所以InnoDB只能从引擎中一行一行的读取，进行统计。

- 同时，InnoDB使用的B+树，纪录都会存在叶子节点中，因此进行count(*)时，会选取最小的B+树进行统计(因为统计出来的结果都是一样的)

 >所以，如果需要频繁的统计行数，你只能自己实现

4.`实现方案`：

- 使用缓存来保存计数
- 使用数据库来保存计数

### 使用缓存来统计

1.`原理`：可以使用Redis来统计，新增数据就 + 1， 删除就 - 1

2.`问题`：

- Redis需要进行持久化，写入磁盘。如果某个时刻Redis宕机，内存中的数据就会丢失，从磁盘中读取出的行数就不准确。如果有这种情况，就再执行一次count(*)来保证数据正确。因为Redis宕机的情况不多

- Redis的结果并不准确。

  - 有可能出现MySQL的会话A插入数据后，redis未+ 1，这时候会话B马上读取行数；

    ![image](https://tva1.sinaimg.cn/large/0085EwgIgy1gtfflytn91j60o0097my002.jpg)

  - 如果先加1，再插入，也会不一致。

    ![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gtffn6etqfj60o40apq3s02.jpg)

### 使用数据库统计

是否可以用新建一张表来统计行数呢？

可以。

- Redis出现的宕机问题，InnoDB如果重启从磁盘中读取的数据会是正确的，此问题pass

- Redis出现的多线程访问造成数据不准确问题，InnoDB的事务也能解决。(事务导致的问题，使用事务自己解决了😀)

  ![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gtffr1hqmfj60nh0d60tt02.jpg)