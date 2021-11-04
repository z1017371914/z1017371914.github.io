## 线程安全问题

HashMap不是线程安全的。

```java
public class HashMapInfiniteLoop {  
	// map初始化为一个长度为2的数组，loadFactor=0.75，threshold=2*0.75=1
    // 也就是说当put第二个key的时候，map就需要进行resize。
    private static HashMap<Integer,String> map = new HashMap<Integer,String>(2，0.75f);  
    public static void main(String[] args) {  
        map.put(5， "C");  

        new Thread("Thread1") {  
            public void run() {  
                map.put(7, "B");  
                System.out.println(map);  
            };  
        }.start();  
        new Thread("Thread2") {  
            public void run() {  
                map.put(3, "A);  
                System.out.println(map);  
            };  
        }.start();        
    }  
}
```

- thread1执行到transfer的Entry next = e.next时，thread2进行了resize。此时thread1的e = key3， next = key7

![image](https://tvax1.sinaimg.cn/large/0085EwgIgy1gthwncb770j612k0hg42v02.jpg)

- 而后进行了线程调度，切换回了thread1，执行了newTable[i] = e, `e = next`。这就使得 e = key7，下一次循环时 next = e.next时，next = key3

![image](https://tvax3.sinaimg.cn/large/0085EwgIgy1gthwr1ws7tj60rv0a676102.jpg)

- 接着进行thread1的 e.next = newTable[i]

  ![image](https://tva1.sinaimg.cn/large/0085EwgIgy1gthwtjzjh7j60sj09075y02.jpg)

  

- 这时候key3.next 为key7.但是key7.next也是key3.**形成了环形链表！！！**

![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gthwtv8ab2j612g0deju502.jpg)

