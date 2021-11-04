

## 数据结构

### SDS

SDS：Simple dynamic string，简单动态字符串。

Redis表示不可变的东西时，或者说简单的打印日志，使用的是C语言的字符串。而自己实际存储的字符串，是一种叫做SDS的字符串。

#### 底层结构

![image](https://tva3.sinaimg.cn/large/0085EwgIgy1goqnnpzzyqj30ls06fmy8.jpg)

是一个C语言中的结构体，有len，free两个int类型，还有一个字符数组。

SDS还是像C语言一样，字符后以'\0'结尾，方便直接使用C语言的一些库函数。

#### SDS与C语言字符串对比

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1goqokoqbb8j30m10610uy.jpg)



|                                | C语言字符串                                                  | Redis                                                        | 概念                                                         | 结果                                                         |
| ------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 获取字符串长度                 | O(N)                                                         | O(1)，不断维护结构体中len的值                                |                                                              | Redis的strlen指令的时间复杂度是O(1)                          |
| 字符串拼接是否会导致缓冲区溢出 | 会，如果用户不对字符串拼接进行检查就会溢出。                 | SDS API对SDS进行修改时，会自动检查空间是否足够。不够的话会自动申请足够的内存来完成凭借操作 | 缓冲区溢出：拼接的字符串覆盖了不该不属于该数组的内存空间     | Redis完全避免了缓冲区溢出的现象。                            |
| 修改字符串带来的内存重分配次数 | 拼接：通过内存重分配增加字符数组大小；截断：通过内存重分配来释放掉不需要的空间，防止内存泄漏。可见每次更改都需要内存重分配 | Redis提供了改进：buf的长度不一定就是字符串长度+1，也还有预分配的多余的内存空间，通过free记录。因此能大大降低内存重分配的次数。优化策略有：空间预分配，惰性空间 | 内存重分配：涉及到复杂的算法，还有可能执行系统调用，开销大，因此次数越少肯定就越好 | Redis减少了内存重分配的次数，对于读写频繁的操作场景，也能发挥很好的效果。 |
| 空间预分配                     | 无                                                           | SDS API对SDS修改时，不仅申请SDS所需的空间，还会适当分配多余的空间。当len小于1MB时，分配相当大小的多余空间，使得len = free；len大于2MB时，每次申请1MB，即free = 1MB | 用于Redis字符串拼接操作                                      | 显著的减少了内存重分配的次数。                               |
| 惰性空间释放                   | 无                                                           | 尽管redis进行截断的操作，但是空余出来的内存并没有释放，所以称为惰性。 | 用于Redis字符串截断操作                                      | 当下次需要拼接时，可能就不需要内存重分配；同时也使得截断操作每次都要内存重分配 |
| 二进制安全                     | c语言用’\0‘来判断字符串的末尾，因此字符串的中间不能保存’\0‘，否则就相当于发生了截断 | Redis不会出现这种问题。它有len字段来声明字符串长度           |                                                              | C只能保存文本文件，Redis能保存任意格式的二进制。             |
| 库函数<String.h>               | 全都能使用                                                   | 只能使用一部分                                               |                                                              |                                                              |

#### SDS API

| 函数        | 作用                                                      | 时间复杂度                                |
| ----------- | --------------------------------------------------------- | ----------------------------------------- |
| sdsnew      | 创建C语言字符对应的SDS                                    | O(N)                                      |
| sdsempty    | 创建一个空的SDS                                           | O(1)                                      |
| sdsfree     | 释放SDS                                                   | O(N)                                      |
| sdslen      | 返回SDS中的len字段，即获取字符串长度                      | O(1)                                      |
| sdsavail    | 返回SDS中的free字段，即获取空闲的内存                     | O(1)                                      |
| sdsdup      | 创建一个SDS的副本，即copy操作                             | O(1)                                      |
| sdsclear    | 清空SDS中的字符串内容                                     | O(1),因为惰性空间分配，所以并没有释放内存 |
| sdscat      | 将C字符串拼接到SDS字符串的末尾                            | O(N)                                      |
| sdscatsds   | 将SDS拼接到某一SDS的末尾                                  | O(N)                                      |
| sdscpy      | 将C字符串覆盖的写入到SDS中的字符串                        | O(N)                                      |
| sdsgrowzero | 用空字符串拓展到给定的长度                                | O(N)                                      |
| sdsrange    | range内的字符串会保留，其他的全都被覆盖或者删除           | O(N)                                      |
| sdstrim     | 接受两参数，其中一个是c字符串，将SDS中的该C字符串全都删掉 | O(N^2),N为C字符串的长度                   |
| sdscmp      | 对比SDS的字符串是否相等                                   | O(N),N为较短的那个字符串                  |



### 链表

c语言并没有链表，因此Redis需自己实现。

Redis中的应用：一个键对应的value有很多个。

#### 底层结构

![image](https://tva3.sinaimg.cn/large/0085EwgIgy1goqp9hed63j30jg09djt2.jpg)

很明显，是个双向链表。但是这还不够，再加入一个list，那么对于链表的整体结构就能更好的控制。

**list：**

![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1goqp7blnqjj30em099wfh.jpg)

![image](https://tvax3.sinaimg.cn/large/0085EwgIgy1goqp8kbuwtj30h406c0tp.jpg)

可见，链表是通过双向链表和一个list来管理。

#### 性质

>1.**无环**。head节点的pre和tail的next都指向null
>
>2.**双端**。使用的pre和next指针，访问前一个数据为O(1)
>
>3.带头指针和尾指针。获取的时间复杂度为O(1)
>
>4.带有链表长度计数器，获取长度时间复杂度为O(1)
>
>5.多态。节点使用(* void)指针来保存节点值，可以在dup，match，free的节点值设定特殊的函数。

#### API（待完善）

| 函数                | 作用                                     | 时间复杂度 |
| ------------------- | ---------------------------------------- | ---------- |
| listSetDupMethod    | 将链表的节点复制函数更新为给定的函数     | O(1)       |
| listGetDupMethod    | 获取当前链表正在使用的节点复制函数       | O(1)       |
| listSetFreeMethod   | 将链表的节点释放函数更新为给定的函数     | O(1)       |
| listGetFree         | 获取当前链表正在使用的节点释放函数       | O(1)       |
| listSetMatchMethod  | 将链表的节点匹配函数更新为给定的函数     | O(1)       |
| listGetMactchMethod | 获取当前链表正在使用的节点匹配函数       | O(1)       |
| listLength          | 获取链表的长度                           | O(1)       |
| listFirst           | 获取链表的头节点                         | O(1)       |
| listLast            | 获取链表的尾节点                         | O(1)       |
| listPrevNode        | 获取当前节点的前一节点                   | O(1)       |
| listNextNode        | 获取当前节点的后一节点                   | O(1)       |
| listNodeValue       | 获取当前节点的节点值                     | O(1)       |
| listCreate          | 创建一个不含任何节点的链表               | O(1)       |
| listAddNodeHead     | 在表头插入一个新的节点作为新的表头       | O(1)       |
| listAddNodeTail     | 在表尾插入一个新的节点作为新的表尾       | O(1)       |
| listInsertNode      | 将新节点插入到当前节点之前或者之后       | O(1)       |
| **listSearchKey**   | 查找并返回包含某个定值的所有节点         | **O(N)**   |
| **listIndex**       | 返回给定节点在链表中的索引               | **O(N)**   |
| **listDelNode**     | 从链表中删除掉给定的节点                 | **O(N)**   |
| **listRotate**      | **将尾节点弹出，放到表头，成为新的表头** | **O(1)**   |
| listDup             | 复制一个给定链表的副本                   | O(N)       |
| listRelease         | 释放链表的内存                           | O(N)       |



### Map

key-value的映射。

例如

```sql
set msg "hello"
```

msg就是key，hello就是对应的value。

#### 底层结构

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1goqpwt2t5hj30dx09cab4.jpg)

table是一个数组，里面的元素存放的是指向dictEntry的指针。

哈希表的结构：

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1goqq08oz6jj30c707dwfb.jpg)

size：记录table的大小

sizemask：总是为size - 1，通过hashCode和sizemask来决定某个key应该放到那个table中的索引中。

used：哈希表中已经有的key的个数。

--------------------------------

哈希节点使用的是dictEntry。结构如下：

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1goqq4pzbvhj30ev090dgl.jpg)

可以发现，value可以是一个指针(*val),可以是uint_64的整数，也可以是int64的整数。

![image-20210320221126925](https://tva1.sinaimg.cn/large/0085EwgIgy1gorec2n5jpj30mc07ddhe.jpg)

但这上面只展示了哈希表，其实和链表一样，redis里的map也有一个数据结构来管理哈希表，也就是一个叫做dict的结构体

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1goqqc3h7uzj30lk09bq42.jpg)

privdata：保存了某些特定函数的可选参数。

ht：是两个元素的数组。每一个数组的元素都是哈希表，即上面的dictht。一般只使用第一个，第二个是在ht[0]进行rehash才使用。

rehashidx：没有使用时，值为-1.记录了rehash的进度。

type：指向了dictType类型的结构体，该结构体封装了很多函数，方便进行某些功能。

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1goqqg988baj30nd0cgdi8.jpg)

综上，redis里实际的map是这样的：

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1goqqljxpymj30ms0cv76l.jpg)

#### 哈希算法

插入过程：

1.首先通过键值，计算出对应的hashCode和索引值。

![image](https://tvax3.sinaimg.cn/large/0085EwgIgy1goqqpyr1dij30dp03sq3r.jpg)

上面说的type和sizemask的作用就体现出来了。type相当于时对函数的封装，sizemask则是计算索引时需要的参数。

2.根据索引值，将新的key-value加入到对应的dictht中的table中。

例如这个例子：

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1goqqteoo8bj30pe0fxwif.jpg)

#### 哈希冲突

哈希冲突：两个或者更多的键，通过计算索引，映射到了同一个索引上面。

解决方法：Redis使用拉链法。

不同于HashMap使用的是尾插法，Redis使用的是头插法，这是基于速度的考虑。

#### Rehash

为了让哈希因子维持在正常的范围，当键值对过多或者过少的时候，应当进行rehash一下，对哈希表进行适当扩大或者缩小。

过程：

>1.为ht[1]分配内存空间：
>
>如果是扩大操作，那么就获取ht[0]的used值，为ht[1]分配第一个2的次方数，该数满足大于等于ht[0].used * 2。说人话，分配内存大小为ht[0]已经使用的数组大小的两倍，同时要向上取2的次方。
>
>如果是缩小操作，那么就分配ht[0].used的向上取的二次方数。
>
>2.将保存在ht[0]的所有key-value都转移到ht[1]，转移的时候得进行重新哈希，来放入到新的索引中。
>
>3.将ht[1]置为ht[0],释放ht[0]。在ht[1]那儿新建一个空的哈希表，为下一次rehash做准备。

#### 渐进式Rehash

上面说到将ht[0]重新哈希到ht[1]时，如果数据量很大的话，不是一次性全部都hash获取，不然会导致服务器直接停止服务。而是渐进的，分多次的进行。

**步骤：**

>1.分配ht[1]的内存
>
>2.将rehashidx从 -1 自增变成0
>
>3.将ht[0]中rehashidx对应的索引中的键值对，hash后放入到ht[1]
>
>4.rehashidx自增1
>
>5.不断重复3和4，直到结束。
>
>6.将rehashidx设为 -1，表示rehash结束。

**问题：**

1.在渐进式rehash过程中，如果需要进行查找、删除、更新等操作，要在两个哈希表中进行。比如查找，现在ht[0]查找，后在ht[1]中查找.

2.rehash过程中，新增的键值对不会再放入到ht[0]，而是放到ht[1]，保证ht[0]在被慢慢的减少数量，从而成功释放内存。

#### 哈希表的扩展与收缩

哈希负载因子计算方法：已保存节点数量/哈希数组的长度

满足下面条件的时候，会对哈希表进行拓展操作：

1.服务器没有正在执行的BGSAVE命令，也没有BGREWRITEAOF命名，负载因子大于等于1时；

2.服务器正在执行BGSAVE命令或者BGREWRITEAOF命令，负载因子大于等于5

同理，负载因子小于1时进行收缩。

#### API

| 函数             | 作用                                 | 时间复杂度 |
| ---------------- | ------------------------------------ | ---------- |
| dictCreate       | 创建新的Map                          | O(1)       |
| dictAdd          | 新增一个键值对                       | O(1)       |
| dictReplace      | 新增键值对，如果已经存在就要进行替换 | O(1)       |
| dictFetchValue   | 获取key对应的value                   | O(1)       |
| dictGetRandomKey | 从Map中随机返回一个键值对            | O(1)       |
| dictDelete       | 从Map中删除掉对应的键值对            | O(1)       |
| dictRelease      | 释放Map的内存                        | O(N)       |



### 跳跃表

在Redis中的用途：

>1.实现有序的集合键
>
>2.在集群节点中用作内部数据结构



### 整数

当一个集合只包含整数值，并且集合的元素数量不多时，redis就会将整数集合作为集合键的底层实现。

#### 底层结构

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gotvkb2w7tj30b1056mxi.jpg)

contents数组存放了整数集合的元素，各个项在数组当中**从小到大排序，并且不含重复项。**

虽然contents数组是用int8_t来定义，但是数组的实际类型是有encoding来决定的。

一个具体的例子展示整数集合的逻辑结构

![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gotvohk12ij30cv060wf5.jpg)

#### 升级

当添加新元素到整数集合里面时，如果新元素的类型比当前所有整数集合的元素的**类型**都要长的时候，要进行升级操作，才能将新元素添加到整数集合中去。

升级的过程：

>1.根据新元素的类型，给contents数组分配足够的内存空间
>
>2.将原来的元素转换成新的元素的类型，并保持原来的有序性没有被破坏
>
>3.将新的元素加入到contents中
>
>4.更改len，len自增1，更改encoding



如果新元素的类型小于现有的所有元素，就会把新元素放到索引为0的地方；

如果新元素的类型大于现有的所有元素，就会把新元素放到索引为length-1的地方；

#### 升级的好处

>1.提升整数集合的灵活性。像c语言中，一个数组的元素的类型必须是一样的，不一样的类型不能存入同一个数组。Redis这么设计，能让用户的输入更加随意，而不会发生类型错误。
>
>2.节约内存。如果程序一开始就将contents定义成int64_t，那么会很浪费内存空间。这样设计，就只会在加入更大的元素的时候才会升级，因此可以认为节约了内存。

ps：一旦升级之后就不能再降级了

#### API

| 函数         | 作用                           | 时间复杂度                  |
| ------------ | ------------------------------ | --------------------------- |
| intsetNew    | 创建一个新的整数集合           | O(1)                        |
| intsetAdd    | 将给定的元素添加到整数集合中   | O(N)                        |
| intsetRemove | 从整数集合中移除给定的元素     | O(N)                        |
| intsetFind   | 检查整数集合中时候有某个整数   | O(log N),有序，使用二分查找 |
| intsetRandom | 从整数集合中随机返回一个元素   | O(1)                        |
| intsetGet    | 取出底层数组在给定索引中的元素 | O(1),即访问某个数组元素     |
| intsetLen    | 返回整数集合的元素个数         | O(1)                        |
| inisetBloLen | 返回整数集合占用的内存字节数   | O(1)                        |



### 压缩列表(ziplist)

当**列表键**或者**哈希键**是一些比较小的**整数**或者较短的**字符串**时，redis会使用压缩列表来作为列表键或者哈希键的底层实现。

#### 底层结构

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gotwrp1gcjj30m20aidk5.jpg)

>
>
>zlbytes：记录整个压缩列表所占的字节数
>
>zltail：记录从压缩列表的尾节点到压缩列表的起始地址有多少字节。这样就可以O(1)的获取尾节点的地址
>
>zllen：记录压缩列表的节点个数。当zllen的数值小于65535时，实际值就是zllen的值；等于65535时，只能遍历整个压缩列表才能得出实际的节点个数。
>
>entry：存储的节点
>
>zlend：用特殊的字符标记压缩列表的末端

#### 节点的底层结构

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gotwyfvwqoj30ch02i0sx.jpg)

不管存储的是字符数组，或者是整数值，节点的底层结构都是由三部分组成。

>
>
>1.**previous_entry_length**：记录压缩列表中前一个节点的长度。
>
>2.**endcoding**：记录当前节点的content属性保存的数据类型与长度
>
>3.**content**：保存节点具体的属性值
>
>

**previous_entry_length：**当前一个节点的长度小于254位时，previous_entry_length只占一个字节。当节点大于254位时，previous_entry_length的长度占5个字节，前一个字节位0xFE，即十进制的254，不能用于存储，由后面的四位进行存储。通过previous_entry_length，就可通过一个节点的初始地址，不断寻找，找到头节点。

#### 连锁更新

前面说过。previous_entry_length的长度会根据前一节点是否超过254位而改变。

如果之前节点都是小于254位，那么如果要插入一个超过254的节点到节点头，就会导致后面的所有节点都要更新previous_entry_length，而占用的字节变多，那么就需要不断更新。这就是**连锁更新**。

而每次连锁更新，需要先更新头节点的第一个节点，更新了第一个节点就需要更新第二个节点....依次类推，需要更新N次，每一次都需要内存重分配，因此时间复杂度是O(n^2).

#### API

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1goty928qrfj30ln0e4n2c.jpg)

### 对象

>Redis并没有直接使用上面说的数据结构来实现键值对数据库，而是基于这些数据结构创建了一个**对象系统**，这个系统包含字符串对象、列表对象、哈希对象、集合对象和有序集合对象这五种类型的对象，每种对象都用到了至少一种前面所介绍的数据结构.

#### 对象类型与编码

Redis在执行一个键值对的时候，需要创建两个对象：**键对象**，**值对象**。

例如

```sql
set msg "hello"
```

创建了一个字符串类型的对象，值为"msg"；

创建了一个字符串类型的对象，值为"hello"。

>
>
>Redis中每个对象都由一个redisObject类型来表示。
>
>该结构体由三个属性：
>
>1.type。type就是下面的五种类型
>
>2.encoding。encoding记录了对象使用的编码，也就是对象使用了什么数据结构来进行存储
>
>3.ptr。指向对象的底层数据结构

![redisObject](https://tva3.sinaimg.cn/large/0085EwgIgy1gou0bsv592j30gm08hdgj.jpg)

##### 类型

Redis的键都是字符串对象，值的对象类型有五种。

| 类型         | 名称         | 用TYPE命令会产生的输出 |
| ------------ | ------------ | ---------------------- |
| REDIS_STRING | 字符串对象   | "string"               |
| REDIS_LIST   | 列表对象     | "list"                 |
| REDIS_HASH   | 哈希对象     | "hash"                 |
| REDIS_SET    | 集合对象     | "set"                  |
| REDIS_ZSET   | 有序集合对象 | "zset"                 |

##### 编码

![Redis对象编码](https://tvax2.sinaimg.cn/large/0085EwgIgy1gou0kv9oa7j30qn0cfjxr.jpg)

使用Object Encoding+key，可以查看对象的值的编码方式。

#### 字符串对象

