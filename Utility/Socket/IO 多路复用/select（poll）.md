# select

- 用于在非阻塞中，当一个套接字或一组套接字有信号通知时，通过 select 函数**确定一个或多个套接字（文件描述符）的状态**，实现 IO 多路复用输入/输出模型；

- 原型：

```cpp
#include <WS2tcpip.h>

int select(int maxfd, fd_set *rdset, fd_set *wrset, fd_set *exset
           , struct timeval *timeout);
```

- 参数：
  
  1. `maxfd` : 监视的最大的文件描述符值 +1，遍历是从 0 - 文件描述符 开始遍历的；
  
  2. `rdset`, `wrset`, `exset` : 需要检测的可读, 可写, 异常文件描述符的集合；
  
  3. `timeout` : 监视描述符事件的发生变化时则返回结果，
     
     1. 为 **NULL**，函数置为阻塞状态，直到文件描述符集合中某个文件描述符发生变化才返回结果；
     
     2. 为 **0 秒 0 毫秒**，则函数置为非阻塞状态，不管 socket 会否发生变化均立刻返回继续执行；
     
     3. **大于 0** 时，为超时时长，时间段内一直阻塞，时间耗尽或有事件发生则返回结果。

- 返回：
  
  1. `-1` : 函数异常，将所有描述符集合清空，通过 **errno** 输出或 **GetLastError()** 获取相应的错误代码；
  
  2. `0` : 函数超时；
  
  3. `>0` : 返回值表示为已经准备好的描述符数；

## 原理

> 网络通信过程在**Unix**系统中通常被抽象为文件的读写过程。

- **select** 模型中的一个 socket 文件描述符通常可以看成一个由设备驱动程序管理的一个设备，驱动程序可以知道自身的数据是否可用。
  
  - 该设备支持阻塞操作并实现了一组自身的等待队列，如读/写等待队列用户支持上层（用户层）所需的 **block**（阻塞）和 **non-block**（非阻塞）操作。
  
  - 设备的资源如果可用（可读/可写）则会通知应用进程。反之则会让进程睡眠，等待数据到来的时候，再唤醒应用进程。

- 多个这样的设备的文件描述符被放在一个队列中，然后 **select** 调用的时候遍历这个队列，如果对应的文件描述符可读/可写则会返回该文件描述符（调用应用进程的回调事件）。当遍历结束之后，如果仍然没有一个可用的文件描述符，**select** 会让用户进程睡眠，直到等待资源可用的时候再唤醒用户进程并返回对应的文件描述符（调用应用进程的回调事件）。
  
  > **select** 每次遍历都是线性的（ O(n) ）。

## 监听流程

- I/O 多路复用技术保持第一阶段阻塞状态，通过调用 select（利用内核不断轮询监听其他客户端的 I/O 操作是否完成）实现多个 fd 监听。

<img src="https://ask.qcloudimg.com/http-save/yehe-2905514/sp9l2j3a52.png?" title="" alt="" data-align="center">

## 利

- 使用便利，跨平台，不用多线程可以实现一个服务端对多个客户端（多路复用）；

## 弊

- 随着文件描述数量的增长，用户态和内核的地址空间的复制引发的开销也会线性增长；

- fd_size 大小为 32 个整数，若为 32 位机器，则最大只能同时处理 1024 个 fd（fd/bits）;

- 每次调用 select 之前都会从用户空间中把 FE_SET 复制到 kernel，而且 kernel 还要轮询每个 fd 来判断状态；

- select 模型是线程不安全的。

## 与 poll 的差别

poll 和 select 原理相似，但差别如下：

- 描述 fd 集合的方式不同，poll 使用 **pollfd 结构**而不是 select 的 fd_set 结构，所以 poll 是**链式**的，**没有最大连接数的限制**；

- poll 属于**水平触发**，也就是通知程序 fd 就绪后，当前无处理，下次 poll 的时候会**再次通知同个 fd 已经就绪**。

# fd 宏

## FD_ZERO

```cpp
FD_ZERO(fd_set *fdset)
```

> 将指定的文件描述符集**清空**，在对文件描述符集合进行设置前，必须对其进行初始化，如果不清空，由于在系统分配内存空间后，通常并不作清空处理。

## FD_SET

```cpp
FD_SET(fd_set *fdset) 
```

> 用于在文件描述符集合中**增加**一个新的文件描述符。

## FD_CLR

```cpp
FD_CLR(fd_set *fdset)
```

> 用于在文件描述符集合中**删除**一个文件描述符。

## FD_ISSET

```cpp
FD_ISSET(int fd,fd_set *fdset) 
```

> 用于测试指定的文件描述符是**否存在该集合中**且**是否准备好（可读或可写）**。

# 工作流程

> 单个进程就可以同时处理多个网络连接的 I/O 请求（同时阻塞多个 I/O 操作）

1. FD_ZERO 宏初始化一个 fd_set 对象（初始化 socket 队列）；

2. FD_SET 宏将 socket 文件描述符键入到 fd_set 对象中（加入到 socket 队列）；

3. select 函数调用，等待函数返回（**程序阻塞，kernel 开始轮询，存在即返回，同时系统调用，数据从 kernel 复制到进程缓冲区**）。如果没有套接字返回，那么 select 函数会把 fe_set 对象中的 socket 队列清空。如果有套接字返回，那么将是返回可读/可写/异常的 socket 集合。其余不可读/不可写/无异常的套接字将进行清除；

4. FD_ISSET 对返回的套接字集合进行检查，进行相应操作。

5. 反复执行以上步骤。
- 图示：

![](../../..\image%20cache\select.png)

# 模型理解与示例

## 模型理解

取 fd_set 长度为 1 字节，fd_set 中的每一 bit 可以对应一个文件描述符 fd 。则 1 字节长的 fd_set 最大可以对应 8 个 fd。

1. 执行 fd_set set; FD_ZERO(&set); 则 set 用位表示是 0000,0000；

2. 若 fd＝5，执行 FD_SET(fd,&set); 后 set 变为 0001,0000 (第5位置为1)；

3. 若加入 fd＝2，fd=1，则 set 变为 0001,0011；

4. 执行 select(6,&set,0,0,0) 进入阻塞等待；

5. 若 fd=1,fd=2 上都发生可读事件，则 select 返回，此时 set 变为 0000,0011。
   
   > 注意：没有事件发生的 fd=5 被清空。

## 示例

文件描述符可读测试：

```cpp
int isRead(int fd)
{
    int max_fd = 0;
    fd_set readfds; // 可读描述符
    FD_ZERO(&readfds); // 清空
    FD_SET(fd, &readfds); // 将sockFd添加进入集合内，并更新最大文件描述符
    max_fd = max_fd > fd? max_fd : fd;

    struct timeval timeout;
    timeout.tv_sec  = 0;
    timeout.tv_usec = 0;
    // while(1) {

    int ret = select(max_fd+1, &readfds, nullptr, nullptr, &timeout);
    if (ret < 0) // 未检测到活跃的 fd
        return -1;
    FD_ISSET(fd,&readfds) ? 1 : 0; // 监听到可读（信号）

    // 如果当前套接字为服务端套接字，则就绪等待客户端连接，否则说明接收到消息
    int clientFd; 
    clientFd = accept(serverFd, NULL, NULL);
    // 如果有客户端连接将产生的新的文件描述符添加到集合中,并更新最大文件描述符
    FD_SET(clientFd, &readfds);
    max_fd = max_fd > clientFd ? max_fd : clientFd;

    // }
}
```
