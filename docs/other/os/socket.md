## Socket

1.`socket`：跨网络与不同主机上的进程进行通信

2.`系统调用`

```c
int socket(int domain, int type, int protocal)
```

- domain：协议
- type：通信的特性。比如 SOCK_STREAM 表示的是字节流，对应 TCP、SOCK_DGRAM表示的是数据报，对应 UDP、SOCK_RAW 表示的是原始套接字
- protocal：通信协议，基本废弃，写成0

3.`针对TCP协议通信的socket编程模型`

![image](https://tvax2.sinaimg.cn/large/0085EwgIgy1gtgopw5w59j60md0ix0vn02.jpg)

- server和client初始化socket，获取文件描述符
- server调用bind，绑定IP和端口
- server调用listen，监听端口
- server调用accept，等待client连接
- client调用connect，进行三次握手连接
- server的accept返回用于传输的文件描述符
- client调用write写数据，server调用read读取数据
- client断开时调用close，server就会读取到EOF，读取完毕后调用close进行关闭

4.`针对UDP协议通信的socket编程模型`

![image](https://tva2.sinaimg.cn/large/0085EwgIgy1gtgov5wyzlj60jy0n80v602.jpg)

- 不需要向TCP那样三次握手维护连接
- server和client初始化socket，获取文件描述符
- server和client调用bind绑定IP和端口
- client调用sendto发送，server调用recvfrom接收
- server调用sendto发送，client调用recvfrom接收

5.`针对本地进程间通信的socket编程模型`

本地字节流 socket 和 本地数据报 socket 在 bind 的时候，不像 TCP 和 UDP 要绑定 IP 地址和端口，而是`绑定一个本地文件`