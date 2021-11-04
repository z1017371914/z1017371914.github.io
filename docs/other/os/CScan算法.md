## CScan算法

1.`CScan`：CScan扫描到请求的最大磁道，马上返回到对应的另一端磁道，期间不会相应任何请求。

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1gtkvl8cs9rj60n50jigqe02.jpg)

- 比如磁道从50开始

  请求磁道是 60， 190，30

- 那么磁头的访问顺序：

   50 - > 60 -> 190 -> 0 -> 30

  注意从190到0这个过程中不会响应请求！！！