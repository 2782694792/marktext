# CommManager

- COM 组件，应用与设备间的通讯桥梁；
- 主要功能：支持（多个）串口直接通讯，通过串口分配器通讯以及与 USB 设备的通讯，通讯细节封装进独立的组件。

## 特点

1. 数据打包，解包：在将上层应用传过来的数据通过不同的物理介质发送到设备前，要根据不同的传送介质的规定作相应的打包，接收到的数据要进行解包；
2. 物理发送，接收：使用不同的传送介质, 有不同的传送方法；
3. 发送，接收数据 FIFO 排队；
4. 独占式的访问。

## 使用说明

1. 注册：当**安装 Dunite** 时，CommManager 会自动注册，如果要手工注册，请在命令行输入 Path\CommManager.exe  /regserver，Path 为 CommManager 所在的目录；
2. 在程序中包含 **CommManager.h 和 CommManager_i.c** 两个文件；
3. 在程序中创建 CommManager 组件实例，就可以使用。

## 使用步骤

1. 在程序中包含 CommManager.h 和 CommManager_i.c 两个文件；
2. 实现 ICommEvents，准备接收事件；
3. 在使用 CommManager 的线程中调用 CoInitializeEx **初始化 COM 库**；
4. 调用 **CoCreateInstance 创建实例**，得到 **IComm 接口**；
5. 将实现 ICommEvents 类的实例 Advice 给 IComm 接口；
6. 调用 GetDefaultCommConfig 或者 GetDefaultCommConfig2 **得到缺省的参数 tCommCfg**；
7. 设置 tCommCfg 对应于 InitComm2 需要设置 tCommCfg2 的各成员，**初始化各个通讯参数**；
8. 调用 IComm 接口的方法 InitComm 或者 InitComm2 来**初始化通讯端口**；
9. 调用 SendData 或者 SendData2 发送数据，调用 RecvData 或者 RecvData2 接收数据；  
10. 程序退出时，调用 **CloseComm 关闭打开的通讯端口**；
11. 最后调用 IComm 接口的 **Release 方法释放**资源。 

> 注：
>
> 1. 如果 tCommCfg 或者 tCommCfg2 的成员不能包括所有的通讯参数，应使用它的**扩展成员变量**通过字符串传递；
> 2. 调用 InitComm(InitComm2) 后应**判断其是否成功**； 
> 3. 如果一次设备操作**同时有发送和接收数据**，为了保证接收到的数据是上次发送数据返的结果，必须在**发送前调用 EnterExclusiveSend 或者 EnterExclusiveSend2**，在**接收后调用 ReleaseExclusive 或者ReleaseExclusive2**，保证在发送后、接收之前没有其它线程或程序对同一个设备发送数据。 

### 示例

```cpp
#include "CommManager.h"
#include "CommManager_i.c"

IComm* g_piComm = NULL; 

// 通信组件初始化
BOOL bInitCOMM()
{
	HRESULT hr;
	if (!g_piComm)
	{
		hr = CoCreateInstance(CLSID_Comm, NULL, CLSCTX_LOCAL_SERVER
                              , IID_IComm, (void**)&g_piComm);
		if (FAILED(hr))
		{
			return FALSE;
		}
	} 
	
    // 得到配置
	tCommCfg l_tCfg;
	memset(&l_tCfg, 0, sizeof(tCommCfg));
	l_tCfg.eCommType = COMM_COMBOARD;
	l_tCfg.unCommInfo.sComBoardInfo.sComInfo.iComID = 2;
	hr = g_piComm->GetDefaultCommConfig(&l_tCfg);
	if (FAILED(hr))
	{
		return FALSE;
	}
	l_tCfg.unCommInfo.sComBoardInfo.sComInfo.ByteSize = 8;
	l_tCfg.unCommInfo.sComBoardInfo.sComInfo.BaudRate = 19200;
	l_tCfg.unCommInfo.sComBoardInfo.sComInfo.Parity = NOPARITY;
	l_tCfg.unCommInfo.sComBoardInfo.sComInfo.StopBits = ONESTOPBIT;

	l_tCfg.unCommInfo.sComBoardInfo.iBand = 9600;
	l_tCfg.unCommInfo.sComBoardInfo.iComPort = 8;
	l_tCfg.unCommInfo.sComBoardInfo.iDataLen = 8;
	l_tCfg.unCommInfo.sComBoardInfo.iEventFlag = 0;
	l_tCfg.unCommInfo.sComBoardInfo.iStopFlag = 1;

	hr = g_piComm->InitComm(l_tCfg, false);
	if (FAILED(hr))
	{
		return FALSE;
	}
    return TRUE;
}

BOOL bExecuteCommand(char* p_pCommand, int p_iLen)
{
	int l_iRetLen;
	char l_acRetBuf[256];

	memset(l_acRetBuf, 0x00, sizeof(l_acRetBuf));
	HRESULT hr = g_piComm->SendAndRecvData(p_iLen, (byte*)p_pCommand, 256
                                           , &l_iRetLen, (byte*)l_acRetBuf,
                                           2000, true);
	if (FAILED(hr))
	{
		return FALSE;
	}
	return TRUE;
}
```

## 事件接收

接收设备通讯组件产生的事件，必须实现 ICommEvents 接口，并同事调用 IComm 的 EnableEvents 允许设备通讯组件产生事件。

```cpp
class OCommEventSink : 
public CComObjectRootEx<CComMultiThreadModel>,
public ICommEvents
{
public:
	BEGIN_COM_MAP(OCommEventSink)
	COM_INTERFACE_ENTRY(ICommEvents)
	END_COM_MAP()

	HRESULT __stdcall OnError( 
		/* [in] */ int p_iErrCode,
		/* [string][in] */ char __RPC_FAR *p_pcError);

	HRESULT __stdcall OnDataReceived( 
		/* [in] */ int p_iLen,
		/* [size_is][in] */ byte __RPC_FAR *p_pbData,
		/* [out] */ boolean __RPC_FAR *p_pbRemove) ;
};
```

## 配置结构

### 串口分配器 tComBoardInfo

```cpp
struct tComBoardInfo
{
	int iBand; // 波特率
	int iDataLen; // 数据位长度
	int iEventFlag; // 奇偶校验位
	int iStopFlag; // 停止位
	int iComPort; // 端口号
	struct tComInfo sComInfo; // 串口配置信息
}; 
```

- **iBand**：串口分配器端口的波特率，一般为 1200 的倍数；
- **iDataLen**：串口分配器端口的数据位长度，一般为 8；
- **iEventFlag**：串口分配器端口的奇偶校验位，0-无校验，1-奇校验，2-偶校验；
- **iStopFlag**：串口分配器端口的停止位，0：无  1：有；
- **iComPort**：串口分配器端口的端口号，取值范围为0到35,包括0和35；
- **sComInfo**：串口的配置，因为指定的串口已经初始化，则**直接使用已初始化的接口**。 

### 串口 tComInfo

[DCB （winbase.h） - Win32 应用 |Microsoft学习](https://learn.microsoft.com/en-us/windows/win32/api/winbase/ns-winbase-dcb)

```cpp
struct tComInfo
{
	unsigned int iComID; // 串口号
	unsigned int BaudRate; // 波特率
	unsigned int fParity; // 奇偶校验
	unsigned int fOutxCtsFlow; // 监视 CTS 发送清除信号进行输出流控制
	unsigned int fOutxDsrFlow; // 监视 DSR 数据集就绪信号进行输出流控制
	unsigned int fDtrControl; // DTR 数据终端就绪流控制
	unsigned int fDsrSensitivity; // 通信驱动程序对 DSR 信号
	unsigned int fTXContinueOnXoff; 
	unsigned int fOutX; // 指示在传输过程中是否使用 XON/XOFF 流控制
	unsigned int fInX; // 接收期间
	unsigned int fRtsControl;
	unsigned short XonLim;
	unsigned short XoffLim;
	byte ByteSize;
	byte Parity;
	byte StopBits;
	char XonChar; // 用于传输和接收的 XON 字符的值
	char XoffChar; // XOFF 字符的值
	unsigned int ReadIntervalTimeout;
	unsigned int ReadTotalTimeoutMultiplier;
	unsigned int ReadTotalTimeoutConstant;
	unsigned int WriteTotalTimeoutMultiplier;
	unsigned int WriteTotalTimeoutConstant;
};

// 若取值 -1，则表示使用缺省的值
```

### 通讯端口 tCommCfg

```cpp
struct tCommCfg
{
	tCommType eCommType;
	tCommInfo unCommInfo;
	char *pcExtern;
};

struct tCommCfg2
{
	tCommType eCommType;
	tCommInfo2 unCommInfo;
	char *pcExtern;
};
```

- **eCommType**：当前通讯端口的类型，有以下取值：
  - COMM_COM：串口；
  - COMM_COMBOARD：串口板；
  - COMM_USB：USB ；

- **unCommInfo**：相应通讯类型的配置参数，以下是对应关系：
  - COMM_COM：配置参数是 tComInfo 结构；
  - COMM_COMBOARD：配置参数是 tComBoardInfo 结构；
  - COMM_USB：配置参数是 tUSBInfo 结构；

- **pcExtern**：扩展参数。格式为一系列的"<Name>=<Value>"的字串,Name 自定义的扩展参数名，Value 相应扩展参数值，有多少个参数，就有多少个这样的名值对。

#### 联合 tCommInfo

```cpp
typedef union _UCommInfo
{
	struct tComInfo sComInfo;
	struct tComBoardInfo sComBoardInfo;
	struct tUSBInfo sUSBInfo;
} tCommInfo;

typedef union _UCommInfo2
{
	struct tComInfo sComInfo;
	struct tComBoardInfo sComBoardInfo;
	struct tUSBInfo2 sUSBInfo;
} tCommInfo2;
```

- 根据相应通讯类型的配置参数，tCommInfo 中的对应结构是：
  - COMM_COM：配置参数是 sComInfo；
  - COMM_COMBOARD：配置参数是 sComBoardInfo；
  - COMM_USB：配置参数是 sUSBInfo、sUSBInfo2。

### USB 通讯端口 **tUSBInfo2**

```cpp
struct tUSBInfo2
{
	GUID sGuid;                     // 设备GUID
	unsigned char acSendPipes[50];  // 发送数据管道，以串的形式提供多个
	unsigned char acRecvPipes[50];  // 接收数据管道，以串的形式提供多个
	unsigned char acExtendStr[256]; // 扩展参数
	int iReserve;                   // 保留参数 
};

struct tUSBInfo // 已过时不用
{
	int iReserver1;              // 保留     
	int iReserver2;              // 保留     
	int iReserver3;              // 保留     
	int iReserver4;              // 保留     
};
```

- **sGuid**：USB 设备的接口 GUID；
- **acSendPipes**：发送数据管道，多个发送管道以逗号分开；
- **acRecvPipes**：接收数据管道，多个接收管道以逗号分开；
- **acExtendStr** ：扩展配置串；
- **iReserve**：保留参数。

## 通讯接口

### 获取配置参数

#### GetDefaultCommConfig

获取缺省参数。

```cpp
HRESULT GetDefaultCommConfig(tCommCfg *p_psCommCfg);

// 同时支持串口与 USB 的参数获取
HRESULT GetDefaultCommConfig2(tCommCfg2 *p_psCommCfg);
```

- **p_psCommCfg**：[in, out]，输入时通过此参数指定通讯端口，则返回相应通讯端口的缺省通讯参数；

- > 返回值：S_OK：成功，E_FAIL：失败；

#### GetCurrentCfg

获取当前配置参数。

```cpp
HRESULT GetCurrentCfg(tCommCfg *p_psCommCfg, boolean *p_pbEnableEvents);

// 获取 USB 相关配置
HRESULT GetCurrentCfg2(tCommCfg2 *p_psCommCfg, boolean *p_pbEnableEvents);
```

- **p_psCommCfg**：[out]，返回当前的配置参数；

- **p_pbEnableEvents**：[out]，返回当前是否发送事件的标志；

- > 返回：S_OK: 成功，E_FAIL: 失败。

### 通讯接口

#### InitComm

初始化通讯接口，使用通讯组件发送接收等方法前，必须先调用此方法（重复调用无效，总是使用第一次初始化的结果）。

```cpp
HRESULT InitComm(tCommCfg p_sCommCfg, boolean p_bEnableEvents);

HRESULT InitComm2(tCommCfg2 p_sCommCfg, boolean p_bEnableEvents);
```

- **p_sCommCfg**：[in]，指定要设置的通讯参数；

- **p_bEnableEvents**：[in]，初始为是否发送事件；

- > 返回值：
  >
  > 1. S_OK: 初始化成功；
  > 2. E_FAIL: 初始化失败；
  > 3. S_COMM_USEOLD:  成功，直接使用已初始化的通讯端口，当前通讯端口的参数未变。

#### CloseComm

关闭已经打开的通讯端口（同个端口可能存在多个示例），并释放相关的资源。

```cpp
HRESULT CloseComm(void); // 不保证关闭当前端口，只是减少当前端口的计数
```

- S_OK：关闭成功；
- E_FAIL：关闭失败；
- **S_COMM_USEBYOTHER**：当前端口尚**有其它实例在用**，没有关闭，但当前实例不能再使用此端口。

### 事件

#### EnableEvents

用于设置通讯组件在收到数据时，**是否主动以事件通知**。

```cpp
HRESULT EnableEvents(boolean p_bEnableEvents);
```

- **p_bEnableEvents**：[in]，true 表示充许事件通知，false 不充许事件通知。

> 客户端接收数据：一，主动向通讯组件请求等待数据；二，由通讯组件在收到数据后立即通知客户端，数据保留在数据接收缓冲区，直到被取出或清除。

#### GetEventsEnable

返回当前是否充许产生事件。

```cpp
HRESULT GetEventsEnable(boolean *p_pbEnableEvents); 
```

- **p_pbEnableEvents**：[out]，返回当前是否充许产生事件的状态。

#### OnDataReceived

在接收到数据并且事件是充许发送时，将触发OnDataReceived事件

```cpp
HRESULT OnDataReceived(int p_iLen, byte *p_pbData, boolean *p_pbRemove);
```

- **p_iLen**：[in] 接收数据的p_pbData 数组的 BYTE 数；
- **p_pbData**：[in] 接收的数据数组；
- **p_pbRemove**：[out]  由事件接收者决定是否将接收到的数据从接收缓冲区移除。如果事件没有被接收或接收者置 p_pbRemove 为 false，则接收到的数据一直在数据接收缓冲区，直到客户程序调用 RecvData 将数据取走。

#### OnError

当发生硬件错误或软件错误时，都将触发OnError事件。

```cpp
HRESULT OnError(int p_iErrCode, char *p_pcError);
```

- **p_iErrCode**：[in] 产生的错误号，以下为预定义的错误号：

  - | 错误号               | 描述         |
    | -------------------- | ------------ |
    | E_COMM_COMOPENFAIL   | 串口打开失败 |
    | E_COMM_INVALIDHANDLE | 无效的 ID 号 |
    | E_COMM_SENDFAIL      | 数据发送失败 |

- **p_pcError**：[in]  错误描述。

### 通讯数据

#### SendData

向设备发送数据。此方法只适用于串口通讯。 

```cpp
HRESULT SendData(int p_iLen, byte *p_pbData, long *p_plIden);

// 支持串口与 USB 通讯
HRESULT SendData2(int p_iLen, byte *p_pbData, long *p_plIden
                  , short p_shPipe = 1);
```

- **p_iLen**：[in] 发送数据的BYTE长度；

- **p_pbData**：[in] 要发送的数据；

- **p_plIden**：[out] 返回的标识,以后可以用此标识取消此次发送；

- `p_shPipe`：[in] 发送数据管道编号，缺省从 1 管道发送数据。此值对串口及串口板通讯没有影响。 

- > **返回值**：
  >
  > S_OK：成功；E_FAIL：失败，可能通讯端口未初始化或已关闭，或其它错误。 

#### RecvData

接收来自通讯端的数据．此方法只适用于串口通讯。

```cpp
HRESULT STDMETHODCALLTYPE RecvData(int p_iLen, int *p_piRecvLen
								, byte *p_pbData, int p_iTimeOut = -1);

// 支持串口与 USB 两种通讯
HRESULT STDMETHODCALLTYPE RecvData2(int p_iLen, int *p_piRecvLen
                                    , byte *p_pbData, int p_iTimeOut = -1
                                    , short p_shPipe = 0);
```

- **p_iLen**：[in] 接收数据的 p_pbData 数组的最大 BYTE 数；

- **p_piRecvLen**：[out] 实际接收到的数据的 BYTE 数；

- **p_pbData**：[out]  用于返回接收的数据数组；

- **p_iTimeOut**：[in]  指定的超时，-1 表示永不超时；

- `p_shPipe`：[in]指定从哪一个管道接收数据,缺省从0管道接收数据。此值对串口通讯没有影响。

- > **返回值**：S_OK: 成功；E_FAIL: 失败。

#### SendAndRecvData

SendData 和 RecvData 的合并,发送数据并接收数据返回.此方法只支持串口通讯。 

```cpp
HRESULT SendAndRecvData(int p_iSendLen, byte *p_pbSendData, int _iOutMaxLen
                        , int *p_piRecvLen, byte *p_pbData
                        , int p_iTimeOut = -1, boolean p_bExclusive = 0);

// 支持串口与 USB 通讯
HRESULT SendAndRecvData2(int p_iSendLen, byte *p_pbSendData, int  _iOutMaxLen
                         , int *p_piRecvLen, byte *p_pbData
                         , int p_iTimeOut = -1, short p_shSendPipe = 1
                         , short p_shRecvPipe = 0, boolean p_bExclusive = 0);
```

- **p_iSendLen**：[in] 发送数据的BYTE长度;

- **p_pbSendData**：[in]  要发送的数据；

- **p_iOutMaxLen**：[in]  接收数据的 p_pbData 数组的最大 BYTE 数；

- **p_piRecvLen**：[out]  实际接收到的数据的BYTE数；

- **p_pbData**：[out] 用于返回接收的数据数组；

- **p_iTimeOut**：[in] 指定的超时，-1 表示永不超时；

- `p_shSendPipe`：[in] 发送数据管道编号,缺省从1管道发送数据。此值对串口与串口板通讯没有影响；

- `p_shRecvPipe`：[in] 接收数据管道编号,缺省从0管道接收数据。此值对串口与串口板通讯没有影响。

- **p_bExclusive**：是否使用独占方式发送和接收；

- > 返回值：S_OK: 成功；E_FAIL: 失败。

#### CancelSendData

取消某次 SendData 发送的数据。 

```cpp
HRESULT CancelSendData(long p_lIden, boolean *p_pbRet); 
```

- **p_lIden**：[in]  由SendData返回的一个标识，表示取消哪一次要发送的数据；

- **p_pbRet**：[out] 返回是否已取消；

- > 返回值：S_OK: 成功；E_FAIL: 失败。

#### CancelRecvData

取消 RecvData 的等待，RecvData 将马上返回。 

```cpp
HRESULT CancelRecvData(void);
```

- > 返回值：S_OK: 成功；E_FAIL: 失败。

#### ClearRecvBuf

清空接收缓冲区.此方法只适用于串口通讯。

```cpp
HRESULT ClearRecvBuf(void);

// 支持串口与 USB 通讯
HRESULT ClearRecvBuf2(short p_shPipe = -1);
```

- `p_shPipe`：指定清除哪一管道的缓冲，缺省值-1表示全部管道。此值只对USB设备有用，对串口方式通讯没有影响。

> 在设备通讯组件内，针对**每个通讯端口都有一个接收数据缓冲区**，设备通讯组件总是及时读取通讯端口的数据，并将它存在接收数据缓冲区，直到上层应用将它取走或清除。对设备的操作通常是先向设备发送数据，设备返回响应的数据，**为了保证接收到的数据是设备对上次发送数据的响应，通常先要将原有在接收缓冲区的数据清除**。

### 通讯扩展

#### ExtendComm

扩展的通讯方法，如果当前接口无法满足要求，可以使用此扩展的接口。

```cpp
HRESULT ExtendComm(int p_iExtendType, VARIANT p_varIn
                   , VARIANT __RPC_FAR *p_varOut);
```

- **p_iExtendType**：[in] 扩展通讯方法的类型，当前的取值有：
  - EXTERN_ESCAPECOMM：等于实现API：EscapeCommFunction ；

- **p_varIn**：[in]  输入参数，不同的扩展类型，它们的参数类型是不同的；
- **p_varOut**：[out]  输出参数，不同的扩展类型，它们的参数类型也是不同的。 

### 独占方式

- 独占式的访问对某一个物理通道加下互斥锁, 在某应用独占,  其它对此同一物理通道的独占必须等到互斥锁被释放才能成功；
- 注意:  互斥锁针对同一个物理通道的不同实例,  对其它物理通道没有任何影响；
- 同一个物理通道的范围是: 对于COM, 则是每个不同的COM号为一个物理通道, 对于串口板, 则是COM号加上串口板端口号为一个物理通道.. 

#### EnterExclusiveSend

进入独占方式以发送数据。

```cpp
HRESULT EnterExclusiveSend(int p_iTimeOut);

// 支持串口与 USB 通讯
HRESULT EnterExclusiveSend2(int p_iTimeOut，short p_shPipe);
```

- **p_iTimeOut**：如果已经处在独占方式（由其它拥有者拥有），则必须等待直到其它拥有者释放独占权或超时；
- `p_shPipe`：需要独占发送的管道编号。此参数对串口通讯没有影响。对于 USB, 参数 p_shPipe 指定需要独占 USB 的哪一个管道进行发送。

- > 返回值：S_OK: 成功，已拥有独占方式；E_FAIL: 超时失败。 

#### ReleaseExclusive

释放独占权 。

```cpp
HRESULT ReleaseExclusive(void);

// 支持串口与 USB 通讯
HRESULT ReleaseExclusive2(short p_shPipe);
```

- `p_shPipe`：USB的发送管道编号，些参数对串口通讯没有影响。

- > 返回值：S_OK: 成功；E_FAIL: 失败。