### Basic Paxos

1.`Basic Paxos`用于解决多节点之间如何就某个值（提案 Value）达成共识.

>假设我们要实现一个分布式集群，这个集群是由节点 A、B、C 组成，提供只读 KV 存储服
>务。创建只读变量的时候，必须要对它进行赋值，而且这个值后续没办法修改。因此一个节点创建只读变量后就不能再修改它了，所以所有节点必须要先对只读变量的值达成共识，然后所有节点再一起创建这个只读变量。

2.`Basic Paxos`赋予节点`角色`的概念。每个节点可以是多个角色。

3.`角色`有三种:

>①`Proposer`：提议者。一般是接收到请求的节点。提议者代表的是接入和协调功能，收到客户端请求后，发起`二阶段提交`，进行共识协商。
>
>②`Accepter`：接收者。对每个提议者提议的值进行投票，并存储接受的值。一般集群所有节点都是接收者。
>
>③`Learner`：学习者。接受达成共识的值，不参与投票。一般是`数据备份节点`充当学习者。

4.**达成共识的实现**

>问题场景：存在`客户端1`，`客户端2`,以及三个节点：`节点A`，`节点B`，`节点C`。Client1的提议编号为1，Client2的提议编号为5. (Client的请求是这样的格式(提议编号，写入的值) ​)。同时A，B先收到Client1的请求，C先收到Client2的请求。
>
>**实现过程：**
>
>>1️⃣**prepare阶段**：
>
>>①Client1和Client2都给A，B，C发送自己的`提议编号`,此时并`没有发送写入的值`！
>
>>②A，B收到Client1的提议编号，C收到Client2的提议编号。
>
>>③A，B给提议者回复**之前未收到提议，承诺不再响应编号小于等于1的提议编号，不会通过标号小于1的提案**；C给提议者回复**之前未收到提议，承诺不再响应编号小于等于5的提议编号，不会通过标号小于5的提案**
>
>>![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gsrbdek0lrj30pm09l0vn.jpg)
>
>>④A，B收到Client2为5的提议，C收到Client1为1的提议
>
>>⑤根据承诺，A，B需要响应Client2的提议，于是**回应提议方之前未收到提议，承诺不再响应编号小于等于5的提议编号，不会通过标号小于5的提案**；C根据承诺，**不会回应Client1的提议，将其进行丢弃**。
>
>>![image](https://tvax3.sinaimg.cn/large/0085EwgIgy1gsrbdryzq9j30ol09x771.jpg)
>
>>2️⃣**accept阶段**:
>
>>①Client1和Client2接收到准备响应的消息，将自己的(提议编号，写入的值)发送给节点A，B，C
>
>>![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gsrbfa0ujfj30pc099tbh.jpg)
>
>>②A，B，C收到提案后，根据承诺来写数据。A，B，C**虽然收到Client1的数据，但会遵守承诺，丢弃其提案不执行**。**收到Client2的提案，满足自己的承诺，进行写入**。
>
>至此，完成了达成共识的目的。
>
>tips：如果集群中有`学习者`，接受者通过了提案并写入后，会通知所有学习者进行提案的写入。

5.`Basic Paxos`的容错：当故障节点少于一般时，集群仍能工作。区别于分布式事务，需要所有节点都同意，才能提交。