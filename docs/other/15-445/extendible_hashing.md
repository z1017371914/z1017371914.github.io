## Extendible Hashing

### 原理简介

实现一个自适应的哈希表，只实现扩容，不考虑缩容(缩容其实就是逆着来的扩容，整体设计思想大差不差)。

此自适应哈希表，不同于JDK中的HashMap的实现。

- HashMap使用的是拉链法+红黑树，当超过负载因子需要扩容时，需要将Node数组扩到一倍，然后将原Node元素重新rehash到新的Node数组中(jdk1.8有了改进，不需要rehash，而是直接计算)

- 而自适应哈希表的特别之处在于，当出现overflow需要扩容时，不需要将所有的哈希桶都进行rehash，而是通过一个spilt pointer指向的哈希桶进行扩容，一定程度上减少了扩容需要的开销。

![image](https://tva4.sinaimg.cn/large/0085EwgIgy1gvfz109p69j60qg0epjuw02.jpg)

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1gvfz1imxgsj60qe0ertcw02.jpg)

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gvfz1vobqcj60qd0esdk202.jpg)

![image](https://tva3.sinaimg.cn/large/0085EwgIgy1gvfz25nw7dj60qh0evdki02.jpg)

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gvfz2fmpsij60qa0ev43g02.jpg)

![image](https://tva3.sinaimg.cn/large/0085EwgIgy1gvfz2r749hj60qe0eo0xz02.jpg)

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gvfz33rjxej60qb0ewq8e02.jpg)

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gvfz3bxljuj60qc0ex44r02.jpg)

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gvfz3kl47aj60qe0esgry02.jpg)

核心原理就这几张图，看Andy讲课就能参悟了

### 任务

需实现的接口

```cpp
  // constructor
  ExtendibleHash(size_t size);
  // helper function to generate hash addressing
  size_t HashKey(const K &key) const;
  // helper function to get global & local depth
  int GetGlobalDepth() const;
  int GetLocalDepth(int bucket_id) const;
  int GetNumBuckets() const;
  // lookup and modifier
  bool Find(const K &key, V &value) override;
  bool Remove(const K &key) override;
  void Insert(const K &key, const V &value) override;
```

### 具体实现

#### 元数据

总的来说，分成以下几个部分：

- 一个数组vector（用于存储哈希桶的指针）
- 全局变量globalDepth，声明当前需要的bit位数
- 每个哈希桶都有自己的局部深度，以及一个kvMap存放数据，kvMap能存放的多少由用于传出的大小决定
- 其他的一些元数据：kvMap的大小，当前哈希桶的个数，全局的mutex变量

哈希桶：

```c++
struct Bucket { // hash bucket, store k-v data
        int localDepth; // describe this bucket needed how many bits to locate
        map<K, V> kvMap;

        Bucket(){}
        Bucket(int localDepth) {
            this->localDepth = localDepth;
        }
    };
```

vector以及元数据的定义

```cpp
 vector<shared_ptr<Bucket>> bucketArr; // store the pointer of every bucket
  int globalDepth;
  size_t maxBucketNums;
  int numBuckets; // current numbers of buckets
  mutex latch;
```

