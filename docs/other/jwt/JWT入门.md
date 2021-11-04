

## JWT

### 引入

用户与服务器的通信：

**基于cookie-session的通信**：

1.用户向服务器发送账号与密码

2.服务器通过对比后，在当前session中存入登录的信息，比如用户角色信息，登录时间等等

3.服务器返回给用户一个session_id,写入到用户的cookie

4.用户每次再访问服务器，都通过cookie来获取session_id，传给服务器

5.服务器通过session_id来获取之前的登录信息，从而达到用户身份认证的目的

缺点：系统的拓展性不强。在服务器集群中，需要将session的信息进行共享，使得每台服务器都能读取到该session。

**基于JWT的用户认证：**

用户首次登录，和服务器认证成功后，返回一个JSON对象给用户

```json
{
  "姓名": "张三",
  "角色": "管理员",
  "到期时间": "2018年7月1日0点0分"
}
```

此后用户与服务器进行通信的时候，都需要发送JSON对象给服务器，服务器通过JSON来进行身份的认证。同时服务器生成这个JSON对象时，会加上签名，防止用户篡改JSON对象。

### 引用更好的解释

#### 传统的 session 流程

1. 浏览器发起请求登陆
2. 服务端验证身份，生成身份验证信息，存储在服务端，并且告诉浏览器写入 Cookie
3. 浏览器发起请求获取用户资料，此时 Cookie 内容也跟随这发送到服务器
4. 服务器发现 Cookie 中有身份信息，验明正身
5. 服务器返回该用户的用户资料

#### JWT 流程

1. 浏览器发起请求登陆
2. 服务端验证身份，根据算法，将用户标识符打包生成 token, 并且返回给浏览器
3. 浏览器发起请求获取用户资料，把刚刚拿到的 token 一起发送给服务器
4. 服务器发现数据中有 token，验明正身
5. 服务器返回该用户的用户资料

**区别**

1. session 存储在服务端占用服务器资源，而 JWT 存储在客户端

2. session 存储在 Cookie 中，存在伪造跨站请求伪造攻击的风险

3. session 只存在一台服务器上，那么下次请求就必须请求这台服务器，不利于分布式应用

4. 存储在客户端的 JWT 比存储在服务端的 session 更具有扩展性

   

**JWT的缺点**

服务器不保存session状态，那么使用期间不可能取消令牌和更改令牌权限，只要Token签发了，在有效期内会一直生效

### JWT的token的结构

header.payload.signature

即：头部.负载.签名

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1goak3m58wgj30sf07mmzl.jpg)

header: JSON对象

![image](https://tva1.sinaimg.cn/large/0085EwgIgy1goak5kh8zrj30wr0admye.jpg)

Payload: JSON对象

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1goak6ekblrj30wu0lwwgz.jpg)

signature： 指定密钥，然后通过算法来产生token

![image](https://tvax4.sinaimg.cn/large/0085EwgIgy1goak7nohsjj30y30aydh9.jpg)

