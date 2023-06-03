# UDP 协议

- UDP（User Datagram Protocol，用户数据报协议）是一种**无连接**的（不需要建立连接）、**不可靠**（主机不需要维持复杂的连接状态表）的**传输层**协议；

- 尽管UDP提供标头和有效负载的完整性验证（通过校验和），但它不保证向上层协议提供消息传递，并且UDP层在发送后**不会保留** UDP **消息的状态**；

- UDP 使用具有最小协议机制的**简单无连接通信模型**；

- UDP 提供数据完整性的校验和，以及用于在数据报的源和目标寻址不同函数的端口号；

- **没有握手对话**，因此将用户的程序暴露在底层网络的任何不可靠的方面。如果在网络接口级别需要纠错功能，应用程序可以使用为此目的设计的传输控制协议（TCP）；

- 通常用于需要**快速传输**数据，但对于数据的完整性和可靠性要求不高的场景，如音频、视频、实时游戏等。

## 面向报文

UDP 是 `面向报文` 的，发送方的 UDP 对应用程序交下来的报文，在添加首部后就向下交付 IP 层。UDP 对应用层交下来的报文，不合并，不拆分，仅仅 `保留这些报文的边界` 。

<img src="file:///E:/MarkText/image%20cache/UDP%20面向报文.png" title="" alt="" data-align="center">

## 特点

1. `没有拥塞控制`，网络出现的拥塞不会使源主机的发送速率降低；

2. 支持一对一、一对多、多对一和多对多的交互通信；

3. `首部开销小`，只有8个字节，比 TCP 的20个字节的首部要短。

## 用户数据报

用户数据报 UDP 有两个字段：`数据字段` 和 `首部字段`。首部字段只有8个字节，由四个字段组成，每个字段都是两个字节。

### 1. 首部字段

- `源端口` ：在需要对方回信时。不需要时可用全 0；

- `目的端口`：在终点交付报文时必须使用；

- `长度` ：UDP 用户数据报的长度，其最小值是8(仅有首部)；

- `检验和`：检测 UDP 用户数据报在传输中是否有错。有错就丢弃。

<img src="file:///E:/MarkText/image%20cache/UDP%20用户数据报.png" title="" alt="" data-align="center">

### 2. 端口分用

当运输层从 **IP 层收到** UDP 数据报时，就根据首部中的目的端口，把 UDP 数据报通过相应的端口，**上交**最后的终点——**应用进程**。

<img src="file:///E:/MarkText/image%20cache/UDP%20端口分用.png" title="" alt="" data-align="center">

如果**接受方 UDP** **发现**收到的报文中的**目的端口号不正确**(即不存在对应于该端口号的应用程序)，**就丢弃**该报文，并由网际控制报文协议 ICMP **发送”端口不可达“差错报文**给发送方。

### 3. 伪首部

- 计算检验和时，要在 UDP 用户数据报之前增加 **12 个字节**的`伪首部`；

- 伪首部并不是 UDP 用户数据报真正的首部。只是在计算检验和时，临时添加在 UDP 用户数据报前面，得到一个**临时的 UDP 用户数据报**，检验和就是按照这个临时用户数据报来计算的；

- 既不向下传也不向上递交，而仅仅是为了计算检验和。

## UDP洪水

1. 一种**拒绝服务攻击**，攻击者将大量用户数据报协议(UDP) 数据包发送到目标服务器，旨在让该设备的处理和响应能力无力承担;

2. UDP 洪水攻击下，防火墙也可能不堪重负，导致对正常流量拒绝服务。

### 工作原理

- 利用服务器响应发送到其端口之一的 UDP 数据包时所采取的步骤。

- 正常情况下，服务器在特定端口上收到 UDP 数据包时，将通过以下两个步骤进行响应：
  
  - 首先检查是否有任何当前侦听指定端口请求的程序正在运行；
  
  - 如果该端口上没有程序正在接收数据包，则服务器将以 ICMP (ping) 数据包作为响应，以告知发送方目标不可达。

<img src="file:///E:/MarkText/image%20cache/UDP%20洪水攻击.png" title="" alt="" data-align="center">

### 防止攻击

1. `配置防火墙`：在防火墙上配置 UDP 报文过滤规则，**只允许指定 IP 或网络范围的 UDP 流量通过**，并限制每个连接的速率和数量。这可以有效地减少无效 UDP 流量的传输，防止洪水攻击的发生。

2. `加强服务器安全性`：提高服务器的安全性，**加强系统权限控制、口令管理等方面的安全策略**，以避免攻击者利用漏洞或弱点进行攻击。

3. 使用`反向代理服务器`：使用反向代理服务器可以**将流量转发到后端服务器**，同时实现简单的负载均衡和筛选，从而过滤掉一些无效的 UDP 流量。

4. `限制源地址的数据报文量`：可以对每个源地址设定一个数据报文限制值，超过该值的会被直接过滤掉。这样就能够限制单个攻击主机产生的攻击流量。

5. `设置流量监测和警报系统`：可以通过设置流量监测和警报系统，**及时发现异常的 UDP 流量**，并进行相应的处理，从而保障服务的正常运行。

# UDP 通讯流程

<img src="file:///E:/MarkText/image%20cache/UDP%20通信流程图.png" title="" alt="" data-align="center">

## 服务端设计

```cpp
#include <iostream>
#include <string.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <unistd.h>
using namespace std;

int main() { 
    // 1 初始化
    int sockfd = socket(AF_INET, SOCK_DGRAM, 0);
    if (sockfd < 0) {
        cout << "Error opening socket" << endl;
        return -1;
    }

    // 2 绑定端口和 IP
    struct sockaddr_in server_addr, client_addr;
    memset(&server_addr, 0, sizeof(server_addr));
    server_addr.sin_family = AF_INET;
    server_addr.sin_addr.s_addr = INADDR_ANY;
    server_addr.sin_port = htons(12345);
    if (bind(sockfd, (struct sockaddr*)&server_addr, sizeof(server_addr)) < 0) {
        cout << "Error binding socket" << endl;
        return -1;
    }

    // 3 接收与发送
    char buffer[1024] = {0};
    socklen_t client_addr_len = sizeof(client_addr);
    while (true) {
        memset(buffer, 0, sizeof(buffer));
        int n = recvfrom(sockfd, buffer, sizeof(buffer), 0
                    , (struct sockaddr*)&client_addr, &client_addr_len);
        if (n < 0) {
            cout << "Error receiving data" << endl;
            break;
        }
        cout << "Received message: " << buffer << endl;

        n = sendto(sockfd, buffer, strlen(buffer), 0
                    , (struct sockaddr*)&client_addr, client_addr_len);
        if (n < 0) {
            cout << "Error sending data" << endl;
            break;
        }
    }

    // 5 关闭并释放
    close(sockfd);
    // WSACleanup();
    return 0;
}
```

## 客户端

```cpp
#include <iostream>
#include <string.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
using namespace std;

int main() { 
    // 1 初始化
    int sockfd = socket(AF_INET, SOCK_DGRAM, 0);
    if (sockfd < 0) {
        cout << "Error opening socket" << endl;
        return -1;
    }

    // 2 确定对方端口号和IP
    struct sockaddr_in server_addr;
    memset(&server_addr, 0, sizeof(server_addr));
    server_addr.sin_family = AF_INET;
    server_addr.sin_addr.s_addr = inet_addr("127.0.0.1");
    server_addr.sin_port = htons(12345);

    // 3 发送服务端
    char buffer[1024] = {0};
    const char* message = "Hello from client";
    strcpy(buffer, message);
    int n = sendto(sockfd, buffer, strlen(buffer), 0, (struct sockaddr*)&server_addr, sizeof(server_addr));
    if (n < 0) {
        cout << "Error sending data" << endl;
        return -1;
    }

    // 4 接收服务端
    memset(buffer, 0, sizeof(buffer));
    socklen_t server_addr_len = sizeof(server_addr);
    n = recvfrom(sockfd, buffer, sizeof(buffer), 0, (struct sockaddr*)&server_addr, &server_addr_len);
    if (n < 0) {
        cout << "Error receiving data" << endl;
        return -1;
    }
    cout << "Received message: " << buffer << endl;

    // 5 关闭并释放
    close(sockfd);
// 
    return 0;
}
```
