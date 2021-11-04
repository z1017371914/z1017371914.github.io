## HashMap扩容机制

1.`公共类`：Map

![img](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2016/f7fe16a2.png)

Map下有四个常用的实现类：

- HashMap
- HashTable：线程安全，但逐渐废弃，并发度不如ConcurremtHashMap
- LinkedHashMap：HashMap的子类，保存了记录的插入顺序
- TreeMap 

2.`HashMap的数据结构`：数组 + 链表 + 红黑树

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gthu53ygayj60qy0ke40d02.jpg)

3.`数组存放的元素`：

- 数组存放的是哈希桶，Node[] table

4.`Node是什么`：

- Node[]是哈希桶数组，存放Node元素
- Node[]数组初始化时，length = 16

```java
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;    //用来定位数组索引位置
        final K key;
        V value;
        Node<K,V> next;   //链表的下一个node

        Node(int hash, K key, V value, Node<K,V> next) { ... }
        public final K getKey(){ ... }
        public final V getValue() { ... }
        public final String toString() { ... }
        public final int hashCode() { ... }
        public final V setValue(V newValue) { ... }
        public final boolean equals(Object o) { ... }
}
```

Node是一个实现了Map.Entry接口的内部类，简而言之就是kv键值对。

5.`Hash冲突`：HashMap使用链地址法来解决。

6.`HashMap的关键参数`：

```java
int threshold;             // 所能容纳的key-value对极限 
final float loadFactor;    // 负载因子，默认是0.75
int modCount;              // 记录HashMap内部结构发生变化的次数
int size; 
```

threshold = loadFactor * Node[].length

当HashMap中node的数量`size  > threshold`时就需要`扩容`

7.`扩容`：

- 初始时设计Node[]数组的个数为 16， 即2  ^ 4。(hashmap规定table的长度必须时 2 ^ n).

  主要是减少冲突，同时定位索引的位置时，能通过位运算来快速定位。

- 拉链法的转表：当链表长度超过8时，就转换为[红黑树](https://blog.csdn.net/v_july_v/article/details/6105630)

