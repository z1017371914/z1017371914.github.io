## LRU

实现一个线程安全的LRU页面置换算法。用于在`bustub`中，对buffer pool中的页面进行置换。

### 接口

```c++
LRUReplacer();

  ~LRUReplacer();

  void Insert(const T &value);

  bool Victim(T &value);

  bool Erase(const T &value);

  size_t Size();
```



### 实现思路

LRU算法，Leetcode有类似的题目，所以很容易实现。

只需要一个**Map + LinkedList**即可实现

## 具体实现

### 元数据定义

链表节点的定义，存储实际的数据，类型是范型T。

```c++
struct Node {
        Node(){};
        Node(T val):val(val) {}; // construct func
        T val;
        shared_ptr<Node> prev;
        shared_ptr<Node> next;
    };
```

哈希表的定义，存储T与链表节点指针的映射，用于O(1)的定位到链表节点，进行相应的更改

```c++
unordered_map<T, shared_ptr<Node>> map;
```

同时定义两链表节点，用于定位链表的头尾。这两个节点不存值，只用于`标记`

```c++
shared_ptr<Node> head;
  shared_ptr<Node> tail;
```

### 构造函数

1.构造出头尾节点

2.头尾节点互相指向

```c++
template <typename T> LRUReplacer<T>::LRUReplacer() { // let head and tail pointer to each other
    head = make_shared<Node>();
    tail = make_shared<Node>();
    head->next = tail;
    tail->prev = head;
}
```

### 析构函数

将整个链表置为空指针

```cpp
template <typename T> LRUReplacer<T>::~LRUReplacer() { // garbage collection, and jvm is the best :)
    while (head) {
       shared_ptr<Node> temp =  head->next;
       head->next = nullptr;
       head = temp;
    }

    while (tail) {
        shared_ptr<Node> temp = tail->prev;
        tail->prev = nullptr;
        tail = temp;
    }
}
```

### 插入操作

操作之前需要上锁，来保证线程安全。

不过多介绍，参考Leetcode的LRU即可

```cpp
template <typename T> void LRUReplacer<T>::Insert(const T &value) {
    lock_guard<mutex> lock(latch);  // ensure to thread safe
    shared_ptr<Node> cur;
    if (map.find(value) != map.end()) {
        cur = map[value];
        // free current node from its original location
        shared_ptr<Node> curPre = cur->prev;
        shared_ptr<Node> curNext = cur->next;
        curPre->next = curNext;
        curNext->prev = curPre;
    } else {
        cur = make_shared<Node>(value);
    }

    // move current Node to head Node's next;
    shared_ptr<Node> headNext = head->next;
    head->next = cur;
    cur->prev = head;
    headNext->prev = cur;
    cur->next = headNext;

    map[value] = cur;

    return;
}
```

### 替换操作

```cpp
/* If LRU is non-empty, pop the head member from LRU to argument "value", and
 * return true. If LRU is empty, return false
 */
template <typename T> bool LRUReplacer<T>::Victim(T &value) {
    lock_guard<mutex> lock(latch);

    if (map.empty()) return false;

    //delete the last node from linklist,and delete it from hashmap
    shared_ptr<Node> tailPre = tail->prev;
    tail->prev = tailPre->prev;
    tailPre->prev->next = tail;
    value = tailPre->val;
    map.erase(tailPre->val);

    return true;
}
```

### 删除操作

```cpp
/*
 * Remove value from LRU. If removal is successful, return true, otherwise
 * return false
 */
template <typename T> bool LRUReplacer<T>::Erase(const T &value) {
    lock_guard<mutex> lock(latch);

    // delete target node from linklist and map
    if (map.find(value) != map.end()) {
        shared_ptr<Node> cur = map[value];
        cur->prev->next = cur->next;
        cur->next->prev = cur->prev;
    }
    return map.erase(value);
}

```

