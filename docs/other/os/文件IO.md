## 文件I/O

1.`阻塞I/O`：

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gtl5kver84j60oj0io3zs02.jpg)

- 用户线程执行read时，线程开始阻塞
- 将数据从用户态拷贝到内核
- 内核准备数据，期间线程一直阻塞
- 准备数据结束，将数据从内核态拷贝回用户
- 唤醒用户线程，文件I/O执行结束

2.`非阻塞I/O`：

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gtl5oqvs20j60p00n0q6102.jpg)

- 应用进程执行read，内核数据未准备好，立即返回结果
- 应用进程周期性的轮询内核，直到内核准备好数据
- 内核准备好数据，这时候的read，是需要等待内核将数据从内核态拷贝到用户态，所以也是会有阻塞等待的

3.`I/O多路复用`:

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gtl5z3p54yj60nz0l8aby02.jpg)

- IO多路复用能让用户进程限制性其他的事情，等IO请求完毕，再回来处理
- 但依旧是存在了等待内核拷贝数据的阻塞等待时期

4.`AIO`:异步IO，在IO多路复用的基础上，实现了完全的异步，不需要再等待数据从内核拷贝到用户

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gtl60bk8jgj60on0mnabl02.jpg)

