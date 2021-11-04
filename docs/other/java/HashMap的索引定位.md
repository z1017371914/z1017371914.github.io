### HashMap的索引定位

通过key，获取哈希桶数组索引的位置。

不管是put，get，delete方法，都需要定位哈希桶的位置。

![image](https://tvax3.sinaimg.cn/large/0085EwgIgy1gthunnpv5fj60k204b40l02.jpg)

- 获取到key的hashCode
- 取高位(`无符号右移`16位到低位，从而将高位弄到低位)
- 高位和hashcode进行异或运算

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1gthurq0kbej60xa0j078o02.jpg)