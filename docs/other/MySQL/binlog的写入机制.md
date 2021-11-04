## bin log的写入机制

1.`bin log写入逻辑`：

- 事务执行期间，将日志改动写入到binlog cache中
- 事务提交后，该线程将自己的binlog cache写入到binlog文件中
- 清空binlog cache

![image](https://tva3.sinaimg.cn/large/0085EwgIgy1gtgbybbd4mj60o00fxq7m02.jpg)

2.`binlog cache`：线程私有的一块内存

3.`binlog file`：公有的一块空间，用于持久化到磁盘中

4.`binlog 写入的参数控制`：通过控制sync_binlog的值来控制写入磁盘的时机(也就是图中fsync的时机)

- sync_binlog = 0：事务提交只进行write操作，不进行fsync
- sync_binlog = 1：事务提交后既进行write，也进行fsync
- sync_binlog = N：事务提交后都会write，提交了N个事务时就进行fsync(风险：MySQL宕机，会最多丢失N个日志)