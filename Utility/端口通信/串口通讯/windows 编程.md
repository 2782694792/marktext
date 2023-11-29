## 编程步骤

1. **打开**：CreateFile()
2. **配置**：SetupComm()、SetCommTimeouts()、SetCommState()
3. **置空**：ClearCommError()、PurgeComm()
4. **读写**：WriteFile()、ReadFile()
5. **检索**：GetOverlappedResult()

## 打开串口

- 头文件：**fileapi.h**
- [createFileA 函数 (fileapi.h) - Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-createfilea)
- **CreateFile()**、**CreateFileA**()和**CreateFileW**()的区别：功能上是相同的，但是采用的字符串编码格式不同，A 代表 **ANSI**，W 代表 **UNICODE**；
- 串口名称：假设当前要打开COM10，应该为 “**`\\.\COM10`**”(需要进行转义)；

```cpp
#ifdef UNICODE
#define CreateFile CreateFileW
#else
#define CreateFile CreateFileA
#endif

HANDLE CreateFileA(
  [in]           LPCSTR                lpFileName,
  [in]           DWORD                 dwDesiredAccess,
  [in]           DWORD                 dwShareMode,
  [in, optional] LPSECURITY_ATTRIBUTES lpSecurityAttributes,
  [in]           DWORD                 dwCreationDisposition,
  [in]           DWORD                 dwFlagsAndAttributes,
  [in, optional] HANDLE                hTemplateFile
);

/**
 * @brief
 * 文件或设备名（TEXT("COM6")），使用正斜杠 (/) 或反斜杠 (\) 
 *
 * dwDesiredAccess 访问权限， GENERIC_READ、 GENERIC_WRITE、0（查询元数据：文件、目录、设备属性）
 *
 * dwShareMode 共享模式，读取、写入、删除、所有共享模式、无，默认 0（阻止其他进程在请求权限时打开文件或设备，独占方式）
 *
 * lpSecurityAttributes 指向 SECURITY_ATTRIBUTES 结构的指针，用于确定是否可由子进程继承返回的句柄，可以为 NULL。
 *
 * dwCreationDisposition 对存在或不存在的文件或设备执行的操作（OPEN_EXISTING 仅当文件或设备存在时才打开）
 *
 * dwFlagsAndAttributes 定义文件或设备的属性和标志，默认值： 未设置其他属性 FILE_ATTRIBUTE_NORMAL（表示：无其他特殊属性或标志、可被读取和写入、可与其他进程共享）。为异步 I/O 打开或创建文件或设备 FILE_FLAG_OVERLAPPED 
 *
 * hTemplateFile 具有 GENERIC_READ 访问权限的模板文件的有效句柄。模板文件为正在创建的文件提供文件属性和扩展属性。串口操作时必须设置为NULL。 
 * 
 * @return 失败: INVALID_HANDLE_VALUE
 */
```

### 同步通信

```cpp
HANDLE m_hCom;
m_hCom = CreateFile("COM1", //串口名，COM10及以上的串口名："\\.\COM10"
					GENERIC_READ|GENERIC_WRITE, // 允许读或写
					0,						  // 独占方式
					NULL,
					OPEN_EXISTING,			   // 打开而不是创建
					NULL,					  // 同步方式
					NULL );
```

### 异步通信

```cpp
HANDLE m_hCom;
m_hCom = CreateFile("\\\\.\\COM11", 
				    GENERIC_READ|GENERIC_WRITE, //允许读或写
					0,							//独占方式
					NULL,
					OPEN_EXISTING,				//打开而不是创建
					FILE_FLAG_OVERLAPPED,		//异步方式
					NULL );
```

## 配置串口

### 输入输出缓冲区

- **SetupComm**()：初始化指定通信设备的通信参数

```cpp
BOOL SetupComm(
  [in] HANDLE hFile,
  [in] DWORD  dwInQueue,
  [in] DWORD  dwOutQueue
);

/**
 *  
 * [in] hFile 通信设备的句柄
 * [in] dwInQueue 输入缓冲区大小（以字节为单位）
 * [in] dwOutQueue 输出缓冲区大小（以字节为单位）
 * 
 * @return
 * @retval 成功：非 0
 * @retval 失败: 0
 */
```

### 超时时间

- **间隔超时**：读
- **总超时**：读、写
- 查询：**GetCommTimeouts**，设置：**SetCommTimeouts**

```cpp
BOOL GetCommTimeouts(
  [in]  HANDLE         hFile,
  [out] LPCOMMTIMEOUTS lpCommTimeouts
);

BOOL SetCommTimeouts(
  [in] HANDLE         hFile,
  [in] LPCOMMTIMEOUTS lpCommTimeouts
);

/**
 * 
 * [in] hFile 通信设备的句柄
 * [out] lpCommTimeouts 指向返回超时信息的 COMMTIMEOUTS 结构的指针
 * 
 * @return
 * @retval 成功：非 0
 * @retval 失败: 0
 * 
 */

typedef struct _COMMTIMEOUTS {
    DWORD ReadIntervalTimeout;
    DWORD ReadTotalTimeoutMultiplier; 
    DWORD ReadTotalTimeoutConstant; 
    DWORD WriteTotalTimeoutMultiplier; 
    DWORD WriteTotalTimeoutConstant;
} COMMTIMEOUTS,*LPCOMMTIMEOUTS;

/**
 *  
 * [in] ReadIntervalTimeout 通信线路上下一个字节到达之前允许经过的最长时间（以毫秒为单位）
 * [in] ReadTotalTimeoutMultiplier 读操作的总超时周期的乘数（每个字符的超时）
 * [in] ReadTotalTimeoutConstant 读操作的总超时时间
 * [in] WriteTotalTimeoutMultiplier 写操作的总超时周期的乘数
 * [in] WriteTotalTimeoutConstant 写操作的总超时时间
 * 
 * @return
 * @retval 成功：非 0
 * @retval 失败: 0
 * 
 * @note ReadIntervalTimeout 和 ReadTotalTimeoutMultiplier 设置为 MAXDWORD 并将 ReadTotalTimeoutConstant 设置为大于零且小于 MAXDWORD 的值
 * 如果输入缓冲区中有任何字节， ReadFile 将立即返回缓冲区中的字节
 * 如果输入缓冲区中没有字节， ReadFile 将等待字节到达，然后立即返回
 * 如果在 ReadTotalTimeoutConstant 指定的时间内没有字节到达， ReadFile 将超时
 * 
 */
```

### 属性设置

- **SetCommState**：配置串口波特率，数据位，奇偶校验，停止位等属性；
- GetCommState 获取一个 [DCB (winbase.h) - Win32](https://learn.microsoft.com/zh-cn/windows/win32/api/winbase/ns-winbase-dcb)（接收控件设置信息） 的结构；

```cpp
BOOL GetCommState(
  [in]      HANDLE hFile,
  [in, out] LPDCB  lpDCB
);

BOOL SetCommState(
  [in] HANDLE hFile,
  [in] LPDCB  lpDCB
);

/**
 *  
 * [in] hFile 通信设备的句柄
 * [in, out] lpDCB 指向接收控件设置信息的 DCB 结构的指针
 * 
 * @return
 * @retval 成功：非 0
 * @retval 失败: 0
 */

typedef struct _DCB { ///< 定义串行通信设备的控制设置
  DWORD DCBlength; 				///< 结构的长度（以字节为单位）
  DWORD BaudRate;				///< 波特率
  DWORD fBinary : 1;			///< 二进制模式
  DWORD fParity : 1; ///< 执行奇偶校验检查并报告错误
  DWORD fOutxCtsFlow : 1; ///< 监视 CTS (明确发送) 信号，以用于输出流控制
  DWORD fOutxDsrFlow : 1; ///< 监视 DSR (数据集就绪) 信号以用于输出流控制
  DWORD fDtrControl : 2;		///< DTR (数据终端就绪) 流控制
  DWORD fDsrSensitivity : 1;	 ///< 通信驱动程序对 DSR 信号的状态敏感
  DWORD fTXContinueOnXoff : 1;	 ///< 输入缓冲区位于已满的 XoffLim 字节内，并且驱动程序传输 XoffChar 字符以停止接收字节后继续传输。
  DWORD fOutX : 1;			    ///< 指示传输期间是否使用 XON/XOFF 流控制
  DWORD fInX : 1;				///< 接收期间
  DWORD fErrorChar : 1; ///< 是否将收到的具有奇偶校验错误的字节替换为 ErrorChar 成员指定的字符
  DWORD fNull : 1; ///< 接收时将丢弃 null 字节
  DWORD fRtsControl : 2; ///< RTS (请求发送) 流控制
  DWORD fAbortOnError : 1; ///< 发生错误时，驱动程序会终止所有读取和写入操作，并显示错误状态
  DWORD fDummy2 : 17; ///< 保留值；请勿使用。
  WORD  wReserved; ///< 保留;必须为零。
  WORD  XonLim; ///< 激活流控制以允许发送方传输之前，输入缓冲区中允许使用的最小字节数
  WORD  XoffLim; ///< 激活流控制以禁止发送方之前，输入缓冲区中允许的最小可用字节数
  BYTE  ByteSize; ///< 传输和接收的字节数
  BYTE  Parity; ///< 奇偶校验方案
  BYTE  StopBits; ///< 停止位数
  char  XonChar; ///< 传输和接收的 XON 字符的值
  char  XoffChar; ///< 传输和接收的 XOFF 字符的值
  char  ErrorChar; ///< 用于替换收到奇偶校验错误的字节的字符的值
  char  EofChar; ///< 用于表示数据结束的字符的值
  char  EvtChar; ///< 用于向事件发出信号的字符的值
  WORD  wReserved1; ///< 保留值；请勿使用。
} DCB, *LPDCB;
```

- 示例：

```cpp
DCB dcb;
GetCommState(m_hCom, &dcb); // m_hCom是串口句柄

dcb.BaudRate = 115200; 		// 波特率
dcb.ByteSize = 8; 	   		// 8位数据位
dcb.StopBits = ONESTOPBIT;	// 1位停止位
							// | ONESTOPBIT | 1位停止位 |
							// | ONE5STOPBITS | 1.5位停止位 |
							// | TWOSTOPBITS | 2位停止位 |
dcb.Parity = NOPARITY; 	    // 无校验 
							// | EVENPARITY | 偶校验 |
							// | MARKPARITY | 标号校验 |
							// | NOPARITY | 无校验 |
							// | ODDPARITY | 奇校验 |
							// | SPACEPARITY | 空格校验 |

SetCommState(hComm, &dcb);
```

## 置空

- 清空缓冲区：**PurgeComm**
- 清除设备的错误标志：**ClearCommError**PurgeComm

```cpp
PurgeComm(m_hCom, PURGE_TXCLEAR|PURGE_RXCLEAR|PURGE_TXABORT|PURGE_RXABORT);	// 清空串口缓冲区，终止发送接收异步操作
DWORD dwError;
if (!ClearCommError(g_hCom, &dwError, NULL))
{
	...
}
```

### 清空缓冲区

- 放弃指定通信资源的输出或输入缓冲区中的所有字符；
- 终止对资源的挂起读取或写入操作。

```cpp
BOOL PurgeComm(
  [in] HANDLE hFile,
  [in] DWORD  dwFlags
);

/**
 *  
 * @param [in] hFile 通信资源的句柄
 * @param [in] dwFlags 清空标识
 * - PURGE_RXABORT 终止所有未完成的重叠读取操作，并立即返回 ，即使读取操作尚未完成
 * - PURGE_RXCLEAR 如果设备驱动程序有一个 ，则清除输入缓冲区
 * - PURGE_TXABORT 终止所有未完成的重叠写入操作，并立即返回 ，即使写入操作尚未完成
 * - PURGE_TXCLEAR 如果设备驱动程序有一个 ，则清除输出缓冲区
 * 
 * @return
 * @retval 成功：非 0
 * @retval 失败: 0
 */
```

### 清空错误标志

- 检索有关通信错误的信息并报告通信设备的当前状态；
- 发生通信错误时，调用函数清除设备的错误标志，以启用其他输入和输出 (I/O) 操作；

```cpp
BOOL ClearCommError(
  [in]            HANDLE    hFile,
  [out, optional] LPDWORD   lpErrors,
  [out, optional] LPCOMSTAT lpStat
);

/**
 *  
 * @param [in] hFile 通信资源的句柄
 * @param [out, optional] lpErrors 指向变量的指针，该变量接收指示错误类型的掩码
 * - CE_BREAK 硬件检测到中断条件
 * - CE_FRAME 硬件检测到一个组帧错误
 * - CE_OVERRUN 发生字符缓冲区溢出。 下一个字符将丢失
 * - CE_RXOVER 发生输入缓冲区溢出。 输入缓冲区空间不足，或在文件尾 (EOF) 字符之后接收到字符
 * - CE_RXPARITY 硬件检测到奇偶校验错误
 * @param [out, optional] lpStat 指向 COMSTAT 结构的指针，其中返回设备的状态信息
 * 
 * @return
 * @retval 成功：非 0
 * @retval 失败: 0
 */

typedef struct _COMSTAT {
  DWORD fCtsHold : 1; ///< 传输正在等待 CTS (明确发送) 信号发送
  DWORD fDsrHold : 1; ///< 传输正在等待 DSR (数据集就绪) 信号发送
  DWORD fRlsdHold : 1; ///< 传输正在等待 RLSD (receive-line-signal-detect) 信号发送
  DWORD fXoffHold : 1; ///< 传输正在等待，因为已收到 XOFF 字符
  DWORD fXoffSent : 1; ///< 传输正在等待，因为 XOFF 字符已传输
  DWORD fEof : 1; ///< 已收到文件末尾 (EOF) 字符
  DWORD fTxim : 1; ///< 一个字符排队等待传输
  DWORD fReserved : 25; ///< 保留值；请勿使用
  DWORD cbInQue; ///< 串行提供程序接收但尚未由 ReadFile 操作读取的字节数
  DWORD cbOutQue; ///< 所有写入操作要传输的用户数据的剩余字节数
} COMSTAT, *LPCOMSTAT;
```



## 写串口

- 用于同步和异步操作：**WriteFile**
- 读写串口之前，需要清空串口缓冲区和清除错误；

```cpp
BOOL WriteFile(
  HANDLE  hFile,				///< 文件句柄
  LPCVOID lpBuffer,				///< 数据缓存区指针
  DWORD   nNumberOfBytesToWrite, ///< 写入的字节数（0：null 写入）
  LPDWORD lpNumberOfBytesWritten, ///< 接收使用同步 hFile 参数时写入的字节数
  LPOVERLAPPED lpOverlapped		 ///< OVERLAPPED 结构体指针：包含用于异步或重叠输入、输出的信息
) ;

/**
 *  
 * @param [in] hFile 文件或 I/O 设备的句柄 (例如文件、文件流、物理磁盘、卷、控制台缓冲区、磁带驱动器、套接字、通信资源、mailslot 或管道)
 * @param [in, out] lpDCB 指向接收控件设置信息的 DCB 结构的指针
 * 
 * @return
 * @retval 成功：非 0
 * @retval 失败: 0
 * @retval 异步完成: 0
 */

typedef struct _OVERLAPPED {
  ULONG_PTR Internal; ///< I/O 请求的状态代码
  ULONG_PTR InternalHigh; ///< 为 I/O 请求传输的字节数
  union {
    struct {
      DWORD Offset; ///< 启动 I/O 请求的文件位置的低顺序部分
      DWORD OffsetHigh; ///< 高顺序部分
    } DUMMYSTRUCTNAME;
    PVOID Pointer; ///< 系统保留
  } DUMMYUNIONNAME;
  HANDLE    hEvent; ///< 事件的句柄，该句柄将在操作完成后由系统设置为信号状态
} OVERLAPPED, *LPOVERLAPPED;
```

## 读串口

- 从指定文件或输入/输出设备读取数据：**ReadFile**

```cpp
BOOL ReadFile(
  [in]                HANDLE       hFile, ///< 设备句柄
  [out]               LPVOID       lpBuffer, ///< 数据缓冲区指针
  [in]                DWORD        nNumberOfBytesToRead, ///< 读取最多字节数
  [out, optional]     LPDWORD      lpNumberOfBytesRead, ///< 指向变量的指针
  [in, out, optional] LPOVERLAPPED lpOverlapped ///< 指向重叠的结构
);

/**
 *  
 * @return
 * @retval 成功：非 0
 * @retval 失败: 0
 * @retval 异步完成: 0
 */
```

## 重叠检索

- 检索指定文件、命名管道或通信设备上重叠操作的结果：**GetOverlappedResult**
- 若要指定超时间隔或等待可警报线程，使用 [GetOverlappedResultEx](https://learn.microsoft.com/zh-cn/windows/desktop/api/ioapiset/nf-ioapiset-getoverlappedresultex)、

```cpp
BOOL GetOverlappedResult(
  [in]  HANDLE       hFile,
  [in]  LPOVERLAPPED lpOverlapped,
  [out] LPDWORD      lpNumberOfBytesTransferred,
  [in]  BOOL         bWait
);

/**
 *  
 * @param [in] hFile 文件、命名管道或通信设备的句柄
 * @param [in] lpOverlapped 指向在启动重叠操作时指定的 OVERLAPPED 结构的指针
 * @param [out] lpNumberOfBytesTransferred 指向变量的指针，该变量接收读取或写入操作实际传输的字节数
 * @param [in] bWait 若 lpOverlapped 结构的 Internal 成员STATUS_PENDING，则函数在操作完成之前不会返回。 如果此参数为 FALSE 且操作仍处于挂起状态，则函数返回 FALSE ， GetLastError 函数返回 ERROR_IO_INCOMPLETE。
 *  
 * @return
 * @retval 成功：非 0
 * @retval 失败: 0 获得更多的错误信息，调用 GetLastError
 */
```



## 示例

- [Windows操作系统：串口篇_windows 串口-CSDN博客](https://blog.csdn.net/qq_42330920/article/details/119185717)
- [Windows使用串口API函数串口编程_windows串口编程-CSDN博客](https://blog.csdn.net/WCH_TechGroup/article/details/104264100)

```cpp
// ComDemo.cpp : 此文件包含 "main" 函数。程序执行将在此处开始并结束。
//

#include "pch.h"
#include <windows.h>
#include <iostream>

// 全局变量
HANDLE h_Com; // 串口句柄

// 打开串口
void ConnectCom(LPCTSTR ComName)
{
	COMMTIMEOUTS TimeOuts; //串口设置超时结构体
	DCB		dcb;

	// 打开一个串口设备
	h_Com = CreateFile(ComName, GENERIC_READ | GENERIC_WRITE, 0, NULL,OPEN_EXISTING, NULL, NULL); 
    /// h_Com = CreateFile(ComName, GENERIC_READ | GENERIC_WRITE, 0, NULL, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL | FILE_FLAG_OVERLAPPED, NULL); // 重叠方式

	if (h_Com == INVALID_HANDLE_VALUE)
	{
		std::cout << "串口无法打开" << std::endl;
		return;
	}

    // 设置输入输出缓冲区
	SetupComm(h_Com, 4096, 4096); 
	TimeOuts.ReadIntervalTimeout = 100; // 超时设置
	TimeOuts.ReadTotalTimeoutMultiplier = 500;
	TimeOuts.ReadTotalTimeoutConstant = 0;
	TimeOuts.WriteTotalTimeoutMultiplier = 0;
	TimeOuts.WriteTotalTimeoutConstant = 0;
	SetCommTimeouts(h_Com, &TimeOuts);

    // 设置串口属性
	GetCommState(h_Com, &dcb); // 串口属性配置
	dcb.BaudRate = 115200;
	dcb.ByteSize = 8;
	dcb.StopBits = ONESTOPBIT;
	dcb.Parity = NOPARITY;
	if (!SetCommState(h_Com, &dcb))
	{
		CloseHandle(h_Com);
		std::cout << "串口配置失败" << std::endl;
		return ;
	}
    
    // 清空串口缓冲区
	PurgeComm(h_Com, PURGE_TXCLEAR | PURGE_RXCLEAR | PURGE_TXABORT | PURGE_RXABORT);
}

int main()
{
	ConnectCom(L"COM7"); // 打开指定串口并进行配置
	
	// 发送
	DWORD dwError;
	DWORD dwSend = 0;
	const char* pSendBuf = "test send";
	
	if (ClearCommError(h_Com, &dwError, NULL))
	{
		PurgeComm(h_Com, PURGE_TXABORT | PURGE_TXCLEAR);
	}
    
	if (!WriteFile(h_Com, pSendBuf, strlen(pSendBuf), &dwSend,NULL))
	{
		std::cout << "发送失败" << std::endl;
	}
    
    /*
    
	// 建立一个重叠结构
	OVERLAPPED wrOverlapped;
	ZeroMemory(&wrOverlapped, sizeof(wrOverlapped));
	if (wrOverlapped.hEvent != NULL)
	{
		ResetEvent(wrOverlapped.hEvent);
		wrOverlapped.hEvent = CreateEvent(NULL, TRUE, FALSE, NULL);
	}
	
    if (!WriteFile(h_Com, pSendBuf, strlen(pSendBuf), &dwSend, &wrOverlapped))
	{
		if (GetLastError() == ERROR_IO_PENDING)
		{
			while (!GetOverlappedResult(h_Com, &wrOverlapped, &dwSend, FALSE))
			{
				if (GetLastError() == ERROR_IO_INCOMPLETE)
				{
					continue;
				}
				else
				{
					std::cout << "发送失败" << std::endl;
					ClearCommError(h_Com, &dwError, NULL);
					break;
				}
			}
		}
	}
    */

	std::cout << "已发送: " << dwSend << "个字节" << std::endl;

	// 接收
	DWORD dwWantRead = 100;
	DWORD dwRead = 0;
	char* pReadBuf = new char[100];
	memset(pReadBuf, 0, 100);

	if (ClearCommError(h_Com, &dwError, NULL))
	{
		PurgeComm(h_Com, PURGE_RXABORT | PURGE_RXCLEAR);
	}

	if (!ReadFile(h_Com, pReadBuf, dwWantRead, &dwRead, NULL))
	{
		std::cout << "读取失败" << std::endl;
	}
	std::cout << "读取的内容: " << pReadBuf << std::endl;
	
    /*
    if (!ReadFile(h_Com, pReadBuf, dwWantRead, &dwRead, &wrOverlapped))
	{
		if (dwError = GetLastError() == ERROR_IO_PENDING)
		{
			while (!GetOverlappedResult(h_Com, &wrOverlapped, &dwRead, FALSE))
			{
				if (GetLastError() == ERROR_IO_INCOMPLETE)
				{
					continue;
				}
				else
				{
					std::cout << "接收失败" << std::endl;
					ClearCommError(h_Com, &dwError, NULL);
					return 0;
				}
			}
		}
	}
    */
        
    delete[] pReadBuf;
}
```

