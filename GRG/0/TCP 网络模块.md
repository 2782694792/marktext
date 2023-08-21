

## 1.1 网络模块使用方法

网络模块是一个windows的MFC扩展动态库(DLL)，全部的接口函数均由输出类ONetwork的成员提供。使用ONetwork进行通讯的步骤如下：

1. 在工程适当的地方包含**NetworkDef.h**，**NetworkStruct.h**，**ONetwork.h**头文件；
2. 为工程加入**链接库NetworkDLL.lib**；
3. 声明一个**ONetwork类的实例**；
4. 调用实例的各成员函数实现数据通讯；
5. 运行时需要将**NetworkDLL.dll**拷贝到上层应该程序同一目录下。

## 1.2 ONetwork 类接口成员函数

### 1.2.1. 初始化

函数声明：**int iInitNetwork(int p_iFlag)**

功  能：初始化网络模块，载入相关的动态库
输  入：`p_iFlag`：日志记录方式，1：输出到调试器，2：输出到文件
输  出:   无
返回值: 成功，失败

### 1.2.2. 添加网络会话

函数声明：   **int  iAddNetSession(LPVOID p_pvParam)**

功  能：  新建一个网络会话并加入到管理列表，但不启动该会。会话信息由p_pvParam指定
输  入：  `p_pvParam`：会话信息参数，在TCP协议下，该信息为如下**tNetSession**结构：

```cpp
typedef struct
{
	int  iSessionID;         // 网络连接编号
	int  iFlag;              // 标志：1-服务器、0-客户端
	int  iPortNo;           // 端口号
	int  iLinkMode;         // 连接方式：1-长连接、0-短连接
	char  acIPAddr[IPADDRSIZE]; // IP地址
} tNetSession;
```

输  出:   无
返 回 值:    成功，失败

### 1.2.3. 删除网络会话

函数声明：   **int  iRemoveSession(int p_iFlag, int p_iSessionID)**

功  能：  删除一个网络会话，如果该会话还未关闭，会先关闭该网络会话再做删除。
输  入：  `p_iFlag`：会话类型(0、客户端网络会话，1、服务端网络会话)
               `p_iSessionID`：会话标识号，唯一识别一个网络会话的编号
输  出:   无
返 回 值:    成功，失败 

### 1.2.4. 修改网络会话信息

函数声明：   **int iUpdateNetSession(LPVOID p_pvParam)**

功  能：  **修改**网络会话的信息，**需要先停止**该会话的网络通讯然后**再启动**，修改才会起作用。
输  入：  `p_pvParam`：会话信息参数，在**TCP协议**下，该结构同iAddNetSession函数的第一参数。
输  出:   无
返 回 值:    成功，失败 

### 1.2.5. 启动网络会话

函数声明：   **int  iStartNetworkService(int p_iFlag, int p_iSessionID)**

功  能：  **启动网络会话**，如果是客户端会话则会连接远端服务器，如果为服务端会话则开始在指定的本地端口上进行侦听。
输  入：  `p_iFlag`：会话类型(0、**客户端**网络会话，1、**服务端**网络会话)
			   `p_iSessionID`：会话标识号，唯一识别一个网络会话的编号。-1表示一次启动所有由**p_iFlag**指定类型的网络会话。
输  出:   无
返 回 值:    成功，失败 

### 1.2.6. 关闭网络会话

函数声明：    **int  iStopNetworkService(int p_iFlag, int p_iSessionID)**

功  能：  **关闭**网络会话，如果是客户端会话则断开连接，如果为**服务端**会话则**先关闭所有的客户连接，然后再停止侦听**。
输  入： `p_iFlag`：会话类型(0、客户端网络会话，1、服务端网络会话)
			  `p_iSessionID`：会话标识号，唯一识别一个网络会话的编号。-1表示一次关闭所有由p_iFlag指定类型的网络会话。
输  出:   无
返 回 值:    成功，失败 

### 1.2.7. 发送数据

函数声明：  

```cpp
int iSendData(int p_iFlag, int p_iSessionID, DWORD p_dwIndex, char* p_pcBuf
			, int p_iLen, DWORD p_dwTimeout, BOOL p_bClearRecvBuf = TRUE)
```

功  能：  在**指定的会话上发送**数据。
输  入：  `p_iFlag`：会话类型(0、客户端网络会话，1、服务端网络会话)
			   `p_iSessionID`：会话标识号，唯一识别一个网络会话的编号。
			   `p_dwIndex`：客户连接编号，该参数只在会话为服务端时有效，用于标识发送数据到哪一个客户连接，而该参数的初始值又由iRecvData函数返回得到。
			   `p_pcBuf`,：待发送数据缓冲
			   `p_iLen`：待发送数据大小，以字节数计
			   `p_dwTimeout`：发送超时时间，以毫秒计
			   `p_bClearRecvBuf`：发送前是否清空接收缓冲区，**默认为清空**(TRUE)。
输  出:   无
返 回 值:    成功，失败 

### 1.2.8. 接收数据

函数声明：  

```cpp
int iRecvData(int p_iFlag, int p_iSessionID, DWORD& p_rdwIndex
			, char* p_pcBuf, int* p_piLen, DWORD p_dwTimeout)
```

功  能： 从**指定的会话上接收**数据。
输  入：  `p_iFlag`：会话类型(0、客户端网络会话，1、服务端网络会话)
			   `P_iSessionID`：会话标识号，唯一识别一个网络会话的编号。
			   `p_dwTimeout`：接收超时时间，以毫秒计

输  出:      `p_rdwIndex`：客户连接编号，标识是从哪一个客户端接收到的数据，该参数只在话为服务端时有意义。
                `p_pcBuf`：数据接收缓冲区里包含接收到的数据
                `p_piLen`：指向接收数据的长度
返 回 值:    成功，失败 

### 1.2.9. 获取网络状态

函数声明：  

```cpp
int iGetNetworkStatus(int p_iFlag, int p_iSessionID, int p_iPing = 1)
```

功  能：  **检测**一个**会话的网络状态**，决定是否能够进行数据收发。
输  入：  `p_iFlag`：会话类型(0、客户端网络会话，1、服务端网络会话)
			   `P_iSessionID`：唯一识别一个网络会话的编号。
			   `P_iPing`：**检测方式**，有如下几种方式，**默认为1**：
							1：采用**ICMP协议包**进行检测
							2：采用connect系统调用进行检测
							3：获取**会话的内部状态**进行检测
							**0**：不检测，**直接返回状态正常(**成功)
输  出:   无
返 回 值:    成功，失败

### 1.2.10 获取网络状态2

函数声明： 

```cpp
int iGetNetworkStatus(char* p_pcAddr, int p_iPing = 1, short p_shPort = 0)
```

功  能：  检测到某一个**远端地址的链路状态**，决定是否能够进行数据收发。
输  入：  `p_pcAddr`：**远端IP地址**
			   `P_iPing`：检测方式，有如下几种方式，默认为1：
							**1**：采用ICMP协议包进行检测
							**2**：采用connect系统调用进行检测
							**0**：不检测，直接返回状态正常(成功)
          	 `P_shPort`：远端端口
输  出:   无
返 回 值:    成功，失败 

### 1.2.11 获取会话信息

函数声明：

```cpp
int iGetNetSession(int p_iFlag, int p_iSessionID,tNetSession* p_psNetSession)
```

功  能：  检测到某一个远端地址的链路状态，决定是否能够进行数据收发。
输  入：  `p_iFlag`：会话类型(0、客户端网络会话，1、服务端网络会话)
			   `p_iSessionID`：唯一识别一个网络会话的编号。
输  出:     `p_psNetSession`：tNetSession 结构类型的数据，结构成员同工 iAddNetSession 的一参数。
返 回 值:    成功，失败