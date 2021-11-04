## HashMapd的扩容

`JDK7`:

```java
 1 void resize(int newCapacity) {   //传入新的容量
 2     Entry[] oldTable = table;    //引用扩容前的Entry数组
 3     int oldCapacity = oldTable.length;         
 4     if (oldCapacity == MAXIMUM_CAPACITY) {  //扩容前的数组大小如果已经达到最大(2^30)了
 5         threshold = Integer.MAX_VALUE; //修改阈值为int的最大值(2^31-1)，这样以后就不会扩容了
 6         return;
 7     }
 8  
 9     Entry[] newTable = new Entry[newCapacity];  //初始化一个新的Entry数组
10     transfer(newTable);                         //！！将数据转移到新的Entry数组里
11     table = newTable;                           //HashMap的table属性引用新的Entry数组
12     threshold = (int)(newCapacity * loadFactor);//修改阈值
13 }

```

```java
 1 void transfer(Entry[] newTable) { // transfer()方法将原有Entry数组的元素拷贝到新的Entry数组里
 2     Entry[] src = table;                   //src引用了旧的Entry数组
 3     int newCapacity = newTable.length;
 4     for (int j = 0; j < src.length; j++) { //遍历旧的Entry数组
 5         Entry<K,V> e = src[j];             //取得旧Entry数组的每个元素
 6         if (e != null) {
 7             src[j] = null;//释放旧Entry数组的对象引用（for循环后，旧的Entry数组不再引用任何对象）
 8             do {
 9                 Entry<K,V> next = e.next;
10                 int i = indexFor(e.hash, newCapacity); //！！重新计算每个元素在数组中的位置
11                 e.next = newTable[i]; //标记[1]
12                 newTable[i] = e;      //将元素放在数组上
13                 e = next;             //访问下一个Entry链上的元素
14             } while (e != null);
15         }
16     }
17 } 
```

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gthv8pu8fhj60ti0p64cs02.jpg)

`resize`过程：

- 引用扩容前的数组
- 判断扩容前的数组的length是否等于2^30次方
  - 是：修改它的threshold为2^31 - 1,这样就不会再扩容，然后返回
  - 否：进行下一步
- 初始化一个新的Entry数组，大小为扩容传入的参数
- 调用transfer将数据迁移到新的Entry数组
- 将原来的table引用到新的Entry数组
- 修改threshold为新的阈值

`transfer工作过程：`

- 创建src引用原来的数组

- 获取传入的新数组的长度

- 遍历旧的数组，获取其中的每个元素

- 对于每个元素，如果非空的话

  - 将其在旧的数组上的内容释放(置为空)
  - 获取当前元素的next指针
  - 重新计算该元素在新数组中的位置i(调用indexFor，传入其hashCode和新的容量)

  - 将当前元素的next指针指向计算出的新的数组中的位置newTable[i]
  - 将newTable[i]赋值当前元素，也就是将当前元素的内容，放到了新的newTable对应索引为i的位置
  - 将当前元素变成其next，也就是进入到下一个元素

总的来说，上面扩容时，新的元素在newTable中使用了`头插法`。

引用[美团技术团队](https://tech.meituan.com/2016/06/24/java-hashmap.html)的图

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gthvt48h2pj61120pqwks02.jpg)

- 首先e = key3，next = key7

- 计算e在newTable中的索引，为3，将newTable[3]的内容填充key3

- 此时next依旧是key7，进行e = next，所以变成了e = key7，next = key5

- 进入新一轮的迁移：

  - 计算e在newTable中的索引，为3，将newTable[3]的内容填充key7
  - 此时next依旧是key5，进行e = next，所以变成了e = key5，next = null

- 再进入新一轮的迁移

  - 计算e在newTable中的索引，为1，将newTable[1]的内容填充key5
  - 此时next是null，进行e = next，所以变成了e = null，触发了e != null的条件，退出resize

  

---

以上说的是JDK7的过程，`JDK8有了一些优化`

- 扩容时，我们都是2次幂的拓展，即length * 2.那么这样就会使当前元素在newTable中：
  - 要么在相同的索引
  - 要么在原位置在移动二次幂的位置

比如：

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gthw4bd9ldj619c0ce7ay02.jpg)

- 扩容前，n = 16， 所以 n - 1 = 0000 1111
  - 这时候计算key1的索引(假设)：0000 0101
  - 这时候计算key2的索引(假设)：0000 0101
- 扩容后，n = 32， 所以 n - 1 = 0001 1111
  - 这时候计算key1的索引(假设)：0000 0101
  - 这时候计算key2的索引(假设)：0001 0101
- key1和key2是一样的东西，分开只是为了展现出扩容后，这个key的索引能有多少种情况：无非是两种
  - 和扩容前的索引一致
  - 和扩容后的索引差了原长度

![image](https://tvax3.sinaimg.cn/large/0085EwgIgy1gthw969u76j60tk05m74y02.jpg)

有了这样的结论，我们在确定resize后的索引，就不要调用`indexFor`计算原来的索引啦~

而是直接判断hash值对应的bit位是0还是1.

- 是0的话索引不变
- 是1的话新索引为原索引 + 原来table的长度(比如原来扩容前为16)