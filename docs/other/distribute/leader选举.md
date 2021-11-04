### leader选举

1.`Raft`是基于`Multi-Paxos`的一致性共识算法，也是一切以Leader为中心来决策的。

2.Raft的成员有三种身份：`leader`,`follower`,`candidate`

>follower：接受leader的信息并进行处理。定时和leader进行心跳交流，如果leader失联，就会将自己推荐成candidate
>
>candidate：向其他节点发送投票的信息，如果收到最多的投票，那么候选人就会成为Leader
>
>leader：处理写请求，管理日志复制，发送心跳包。

3.选举leader的过程：

>①初始时刻，所有节点均是`follower`状态,每个节点自己都有随机的心跳超时时间(超过时间仍未收到leader的心跳包，就会毛遂自荐为leader)。
>
>![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gtarrn67q9j30q60k5din.jpg)
>
>2.首次超时的candidate，在这第一次任期中，发送自荐包(带上了任期时间)给其他节点。如果其他节点在此任期内未收到其他leader的消息，就会同意该candidate的自荐。
>
>![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gtaruk2ejij30mc0onq63.jpg)
>
>3.A收到大多数选票，记录自己的选票数，当选Leader。而其他节点会标记他们投票时的任期数，如图中的1。
>
>![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gtaruk2ejij30mc0onq63.jpg)
>
>4.A当选leader后，不断发送心跳包给其他节点来声明”我还活着，请不要同意其他节点的投票“
>
>![image](https://tvax3.sinaimg.cn/large/0085EwgIgy1gtarwrfg6aj30pr0nntby.jpg)

4.整个过程设计到两种RPC：`请求投票RPC`,`日志复制RPC`

>请求投票：超时节点发送请求投票的信息给其他节点。
>
>日志复制：复制日志+发送心跳包

5.任期信息的规则：

>①每个节点都会有自己的任期号。当某节点想自荐时，就会发送他自己的任期号+1的请求投票RPC给其他节点
>
>②每个节点收到比自己大的任期号信息时，会将自己的任期也改成收到所有信息中最大的任期号
>
>③如果某个节点是leader或者candidate，收到了比自己任期大的信息，那么直接降级为follower
>
>④如果某个节点收到了任期号小的请求，那么会直接拒接该请求。
>
>个人拙见：在我看来，任期像是表明这个节点经历了几个回合。它的回合数少，那就说明发生了丢包，他说的话就都不会被算数，一切以经历了最多回合的节点为准。

6.选举的规则：

>①leader周期性发送心跳信息来阻止其他节点进行选举
>
>②如果节点超时了仍未收到leader消息，就会变成candidate，就会向其他节点发送投票请求RPC
>
>③获得票数最多的节点会升级为leader
>
>④follower的投票遵循first come first vote。A收到B的任期为4的RPC，再收到C的任期为4的RPC时会以无选票为理由拒绝C的投票请求。也就是一人一票制，先到先投票。

7.如何保证选举不会出现”无多数票的情况“？

>每个节点都有随机超时时间。这样，大部分情况下都只有一个节点超时，然后自荐为leader。