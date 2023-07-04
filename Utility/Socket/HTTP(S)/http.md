# HTTP

- 超文本传输协议（HyperText Transfer Protocol）：[HTTP 概述 - HTTP | MDN](https://developer.mozilla.org/zh-CN/docs/web/http/overview)；
- 基于**客户端/服务端（C/S）的架构模型**，一种能够获取如 HTML 这样的网络资源的通讯协议；
- 是一种**无状态**协议，协议本身不会对发送过的请求和相应的通信状态进行持久化处理，从而能够处理大量事务；
- 基于 TCP 协议的**应用层传输协议**，通过 [TCP](https://developer.mozilla.org/zh-CN/docs/Glossary/TCP)，或者是 [TLS](https://developer.mozilla.org/zh-CN/docs/Glossary/TLS) —— 加密的 TCP 连接来发送；
- 不仅被用来传输超文本文档，还用来传输图片、视频或者向服务器发送如 HTML 表单这样的信息，还可以根据网页需求，仅获取部分 Web 文档内容更新网页；
- 默认端口号：80，客户端是动态的；

> http 是无状态协议，需要记录管理状态，例如 cookie。

## 发展

[HTTP 的发展 - HTTP | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/Evolution_of_HTTP)

## 五大特点

- **简单快速**；虽然下一代 HTTP/2 协议将 HTTP 消息封装到了**帧**（frame）中，HTTP 大体上还是被设计得简单易读。HTTP 报文能够被人读懂，还允许简单测试；

- ***灵活**，可扩展；在 HTTP/1.0 中出现的 [HTTP 标头（header）](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers)让协议扩展变得非常容易。只要服务端和客户端就新标头达成语义一致，新功能就可以被轻松加入进来；允许传输任意类型的数据对象；

- **支持 B/S、C/S 模式**；

- **无连接**：限制**每次连接只处理一个请求**，服务器处理完请求并收到应答后，断开连接，节省传输时间。

- **无状态**：对于**事务处理没有记忆能力**，在同一个连接中，两个执行成功的请求之间是没有关系的。这就带来了一个问题，用户没有办法在同一个网站中进行连续的交互；
  
  > 而使用 HTTP 的标头扩展，HTTP Cookie 就可以解决这个问题。把 Cookie 添加到标头中，创建一个会话让每次请求都能共享相同的上下文信息，达成相同的状态。

## 控制内容

- `缓存`。
- `开放同源限制`。浏览器强制对 web 网站做了分割限制：只有来源相同的网页才能获取网站的全部信息；HTTP 通过修改标头可以开放这样的限制；
- `认证`。仅让特定访问；
- `代理和隧道`。一般情况下，服务器和/或客户端是处于内网的，HTTP 请求就要通过代理越过这个网络屏障，并非所有代理都是 HTTP 代理；
- `会话`。使用 HTTP Cookie 允许客户端用一个服务端的状态发起请求，创建会话。

## 连接

### 非持久连接

- 对于客户端请求和服务端响应每个请求，如果每次都经过**一个单独的 TCP 连接**发送，则为**非持久连接**；
- **通信开销大**：每次请求响应都要断开再建立新连接；

### 持久连接

- 如果每次都**经过相同的 TCP 连接**进行发送，称为**持久连接**。
- 遇到明确终端要求再中断连接，减少通信开销，**节省通信量**。

![](E:\MarkText\image cache\非-持久连接.png)

# 基于 HTTP 的组件系统

在请求与响应之间，还有许许多多的被称为[代理](https://developer.mozilla.org/zh-CN/docs/Glossary/Proxy_server)的实体，他们的作用与表现各不相同，比如有些是网关，还有些是 [cache](https://developer.mozilla.org/zh-CN/docs/Glossary/Cache) 等。

<img src="file:///E:/MarkText/image%20cache/基于%20HTTP%20的组件系统.png" title="" alt="" data-align="center">

## 代理（Proxy）

1. 在浏览器和服务器之间，有许多计算机和其他设备转发了 HTTP 消息。由于 Web 栈层次结构的原因，它们大多都出现在传输层、网络层和物理层上，还有一部分是表现在应用层上的，被称为**代理**（Proxy）；

2. 代理既可以表现得透明，又可以不透明（“改变请求”会通过它们）；

3. 代理主要有如下几种作用：

- 缓存（可以公开也可以是私有的，像浏览器的缓存）；

- 过滤（像反病毒扫描，家长控制...）；

- 负载均衡（让多个服务器服务不同的请求）；

- 认证（对不同资源进行权限管理）；

- 日志记录（允许存储历史信息）。

# HTTP 流

客户端进行和服务端或中间代理进行信息交互时，有以下过程：

1. 打开 TCP 连接；

2. 发送 HTTP 报文：

   > HTTP 报文（在 HTTP/2 之前）是语义可读的。
   >
   > 在 HTTP/2 中，这些简单的消息被封装在了帧中，这使得报文不能被直接读取，但是原理仍是相同的。

3. 读取服务端返回的报文信息；

4. 关闭连接或者为后续请求重用连接。

## HTTP 和连接

- HTTP **依赖于面向连接的 TCP 进行消息传递**，但连接并不是必须的；

- 在客户端（通常指浏览器）与服务器能够交互（客户端发起请求，服务器返回响应）之前，必须在这两者间建立一个 TCP 链接，打开一个 TCP 连接需要多次往返交换消息（因此耗时）；

- HTTP/1.1 引入了流水线（被证明难以实现）和持久连接的概念：底层的 TCP 连接可以通过 [`Connection`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Connection) 标头来被部分控制。HTTP/2 则发展得更远，通过在一个连接复用消息的方式来让这个连接始终保持为暖连接；

- Google 就研发了一种以 UDP 为基础，能提供更可靠更高效的传输协议 [QUIC](https://en.wikipedia.org/wiki/QUIC)。

# 区别 HTTPS

## HTTP 不足

- **无加密机制**，通信使用明文，内容可能会被窃听；
- **无验证**通信方的**身份**，有可能遭遇伪装；
- **无法证明报文的完整性**，有可能已遭篡改。

## HTTPS

- 通过和 `SSL`(Secure Socket Layer，**安全套接层** )或 `TLS`(Transport Layer Security, **安全层传输协议**)的组合使用，加密 `HTTP` 的通信内容。

- ```text
  HTTP = TCP + IP
  
  HTTPS = HTTP + SSL/TLS
  HTTP + 加密 + 认证 + 完整性保护 = HTTPS（HTTP Secure）
  ```
  
- **混合加密机制**：采用**共享**密钥加密（**对称**）和**公开**密钥加密（非对称，处理速度较慢）；

![](E:\MarkText\image cache\HTTPS 混合加密机制.png)
