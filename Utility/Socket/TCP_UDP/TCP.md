# TCP 协议

- TCP (Transmission Control Protocol)，提供IP环境下的数据可靠传输；

- 服务支持：包括数据流传送、**可靠**性、有效流控、全双工操作和多路复用；

- 通过**面向连接**、端到端和可靠的数据包发送；

- TCP 是一种面向流的协议，意味着当进程向其他进程发送数据时，该数据是一串无界的字符流。

## 协议族

TCP/IP是基于 TCP 和 IP 这两个最初的协议之上的不同的通信协议的大集合；

<img src="file:///E:/MarkText/image%20cache/TCPIP%20协议族.png" title="" alt="" data-align="center">

## 协议的分层

<img src="file:///E:/MarkText/image%20cache/协议的分层.png" title="" alt="" data-align="center">

## 分层的作用

<img src="file:///E:/MarkText/image%20cache/协议分层的作用.png" title="" alt="" data-align="center">

## OSI 7层模型通讯

<img src="file:///E:/MarkText/image%20cache/OSI%207层模型通讯.png" title="" alt="" data-align="center">

# TCP 通讯

## 通讯流程

1. `服务器`**启动**并**绑定**到一个端口，并开始**监听**传入的连接。

2. `客户端`**创建**一个套接字，并使用此套接字**连接**到服务器的 IP 地址和端口。

3. `服务器`**接受**客户端连接，并为客户端**分配**一个新的套接字。

4. 服务器和客户端之间通过它们各自的套接字进行通信，发送和接收数据，直到一方关闭连接。

## 报文、帧、数据包
