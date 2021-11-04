## redo log的写入机制

1.redo log的日志会存在三个地方：

- redo log buffer
- 文件系统的page cache
- 磁盘

![image](https://tva3.sinaimg.cn/large/0085EwgIgy1gtgca41ghfj60o90dg0tu02.jpg)

2.写入的位置由参数决定：innodb_flush_log_at_trx_commit

- innodb_flush_log_at_trx_commit  = 0，事务提交后写入到redo log buffer
- innodb_flush_log_at_trx_commit = 1，事务提交后持久化到磁盘中
- innodb_flush_log_at_trx_commit  = 2，事务提交后写入到page cache中

3.既然上述存在没有写入到磁盘的情况，那么redo log怎么实现持久化?

- 使用一个线程定时的轮询，将redo log buffer中的日志调用write写入到pace cache中，最后调用fsync持久化到磁盘中。



