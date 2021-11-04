## Put方法

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1gthus8koe0j61bo11stns02.jpg)

- 1.执行put方法
- 2.table是否为空(Node[]数组)
  - 2.1是：进行扩容
  - 2.2否：执行下一步
- 3.通过key计算到数组的索引i
- 4.索引i对应的table是否为空
  - 4.1是：直接插入该键值对
  - 4.2否：判断key是否存在
    - 4.2.1是：直接覆盖
    - 4.2.2否：table元素是否是红黑树。是的话直接插入键值对；不是的话遍历链表，如果链表元素小于8，直接插入，大于等于8，转换成红黑树再插入
- 5.插入完毕，判断当前size是否大于threshold？
  - 5.1是：扩容
  - 5.2否：流程结束
- 6.流程结束

```java
 1 public V put(K key, V value) {
 2     // 对key的hashCode()做hash
 3     return putVal(hash(key), key, value, false, true);
 4 }
 5 
 6 final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
 7                boolean evict) {
 8     Node<K,V>[] tab; Node<K,V> p; int n, i;
 9     // 步骤①：tab为空则创建
10     if ((tab = table) == null || (n = tab.length) == 0)
11         n = (tab = resize()).length;
12     // 步骤②：计算index，并对null做处理 
13     if ((p = tab[i = (n - 1) & hash]) == null) 
14         tab[i] = newNode(hash, key, value, null);
15     else {
16         Node<K,V> e; K k;
17         // 步骤③：节点key存在，直接覆盖value
18         if (p.hash == hash &&
19             ((k = p.key) == key || (key != null && key.equals(k))))
20             e = p;
21         // 步骤④：判断该链为红黑树
22         else if (p instanceof TreeNode)
23             e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
24         // 步骤⑤：该链为链表
25         else {
26             for (int binCount = 0; ; ++binCount) {
27                 if ((e = p.next) == null) {
28                     p.next = newNode(hash, key,value,null);
                        //链表长度大于8转换为红黑树进行处理
29                     if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st  
30                         treeifyBin(tab, hash);
31                     break;
32                 }
                    // key已经存在直接覆盖value
33                 if (e.hash == hash &&
34                     ((k = e.key) == key || (key != null && key.equals(k)))) 
35							break;
36                 p = e;
37             }
38         }
39         
40         if (e != null) { // existing mapping for key
41             V oldValue = e.value;
42             if (!onlyIfAbsent || oldValue == null)
43                 e.value = value;
44             afterNodeAccess(e);
45             return oldValue;
46         }
47     }

48     ++modCount;
49     // 步骤⑥：超过最大容量 就扩容
50     if (++size > threshold)
51         resize();
52     afterNodeInsertion(evict);
53     return null;
54 }
```

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gthuzj9sffj60tj11j7ja02.jpg)



![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gthv0nvdwbj60s104e75g02.jpg)