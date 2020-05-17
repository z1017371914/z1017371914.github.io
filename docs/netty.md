# netty

[toc]

## NIO

同步是指 连接和读取是轮着来的，处理完一个连接或者读取才能处理下一个，非阻塞也是因为  连接和读取分开了，不用连接完了，不会因为没有读取到消息而阻塞。可以read的时候肯定是有数据的。

![](https://tva1.sinaimg.cn/large/00831rSTly1gdb5sofotqj30w50ajgob.jpg)

![](https://tva1.sinaimg.cn/large/00831rSTly1gdb6t1ud0sj310t0ebdms.jpg)

创建 serverScoketChannel 和selector

![](https://tva1.sinaimg.cn/large/00831rSTly1gdb639au19j30pv06a43d.jpg)



这里是selector 负责监听是否有channel有连接，有的话，selectionKey就不为空，应该是把serverSocketChannel封装到selectionKey了，因为多个serverSocketChannel可以共用一个selector

![](https://tva1.sinaimg.cn/large/00831rSTly1gdb6d5nca5j30hf07mwh2.jpg)



注意这里要区分是读取事件还是连接事件，ServerSocketChannel是连接的是服务端的channel，SocketChannel是读取的,他是客户端的channel。

 如果是连接的话，就生成一个SocketChannel，并注册selector.

![](https://tva1.sinaimg.cn/large/00831rSTly1gdb6ic16aoj313q0e8ws2.jpg)



![](https://tva1.sinaimg.cn/large/00831rSTly1gdb6zr5dmfj314z0gi13x.jpg)

## netty 线程模型

reactor模型： nio改进，对读写事件有个线程池来管理。

![](https://tva1.sinaimg.cn/large/00831rSTly1gdbydibiovj311e0kwwqc.jpg)

![](https://tva1.sinaimg.cn/large/00831rSTly1gdc20wk03dj30xu0ih14k.jpg)