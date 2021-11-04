### Multi Paxos

1.进行多次`basic paxos`的问题：提案冲突；两次`RPC`造成时延大。

>①进行多次basic paxos，可能会出现无法出现多数票的情况：一个 5 节点的集群，如果 3 
>个节点作为提议者同时提案，就可能发生因为没有提议者接收大多数响应（比如 1 个提
>议者接收到 1 个准备响应，另外 2 个提议者分别接收到 2 个准备响应）而准备失败，需
>要重新协商
>
>②两次RPC(准备阶段+接受阶段)，往返的消息比较多，时延大。

2.`Multi Paxos`相较于`Basic Paxos`的`改进`：**引进了Leader**；**改进Basic Paxos执行过程**

![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gtard8c006j30o90engoi.jpg)

3.改进的执行过程：**当Leader处于稳定状态时，省去准备阶段，以Leader的命令为准，进入到接受阶段**。

这个过程省去了投票阶段的时延，也不会存在投票冲突。

4.写数据：和Leader交互，再由Leader下发命令来进入到接受阶段。

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gtarg6tw8bj30o80dijtx.jpg)

5.读数据：直接返回Leader的值。

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gtargce32xj30p50do0uh.jpg)

6.不足：读写都在Leader上进行，相当于单机性能。