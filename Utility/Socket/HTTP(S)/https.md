# HTTPS

- 默认端口：443；
- 通过和 `SSL`(Secure Socket Layer，**安全套接层** )或 `TLS`(Transport Layer Security, **安全层传输协议**)的组合使用，加密 `HTTP` 的通信内容。

> HTTPS = HTTP + SSL/TLS

## SSL 握手

1. **客户端**提交 https **请求**；
2. **服务器响应**客户，并把**数字证书**和**证书公钥**发给客户端，**客户端**在**本地**找到 **CA 中心的公钥**（根证书），`验证证书公钥`；
3. 客户端验证证书公钥的有效性（**用证书公钥解开数字证书**，解开后**得**到**服务器的公钥**）；
4. 有效后，**生成**一个会话**随机密钥**；
5. `用服务端的公钥加密这个会话密钥`后，发送给服务器；
6. **服务器**收到公钥加密的会话密钥后，**用**自己的**私钥解密**，得到会话密钥；
7. 客户端与服务端**双方利用这个会话密钥加密进行数据传输**。

> 客户端需要预置 CA 签发的根证书（保存了 CA 的公钥）；
>
> 服务器发的是 CA 签发的服务器证书，其中：
>
> 1. 用 CA 私钥对服务器公钥以及其它网站信息加密后得到的密文；
> 2. 对服务器公钥 hash 后的摘要。
>
> 客户端先从 CA 证书获取 CA 公钥，对服务器证书密文进行解密，得到服务器公钥，然后再 hash 一次公钥，比较得到的结果和证书携带的摘要是否一致。

### SSL 证书

1. 客户端对服务器进行身份验证，获取服务器公钥；
2. 加密传输数据，验证传输的数据完整性。

### 服务器数字证书

1. **携带服务器公钥**等信息（域名（持有者）、证书（签发）失效时间等信息）**的明文**和对应 **hash 后的摘要**；
2. **客户端**解密密文得到服务器公钥信息后可以**直接比较服务器证书携带的明文信息**，**确认是否被修改过**；
3. 证书携带摘要：减少发送的数据量，降低发送时长；

## 非对称公开加密

![](E:\MarkText\image cache\非对称公开加密.png)

## CA 证书签发

CA (Certificate Authority，证书授权)是由认证机构服务者签发。

![](E:\MarkText\image cache\CA 验证签发.png)

## 握手过程

1. 服务器获得浏览器公钥；
2. 浏览器获得服务器公钥；
3. 浏览器验证 -> 随机密码 服务器的公钥加密 -> 通信的密钥 通信的密钥 -> 服务器；
4. 服务器用自己的私钥解出随机密码 -> 用密码解密握手消息（共享密钥通信）-> 验证HASH与浏览器是否一致（验证浏览器）。

## 不足

1. 加密解密过程复杂，导致访问速度慢；
2. 加密需要认向证机构付费；
3. 整个页面的请求都要使用 HTTPS。