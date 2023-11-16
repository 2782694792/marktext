## 一、引言

- 设备基类的动态库的名称为：ODevBaseDll.DLL；
- 当前的版本号是：V3.2.1；

### 1.1 安装

- 需安装 Dunite 并注册：ODevBase 依赖于通讯管理组件 GrgCommManager.exe、  配置管理组件 GrgCfgManager.dll 、日志管理组件 GrgLogServer.exe 及日志管理组件配置文件 GrgGrgLogServer.cfg；
- 包含头文件有：CfgManager.h; CfgManager_i.c; LogServer.h; LogServer_i.c; CommManager.h; CommManager_i.c;

### 1.2 输出

1. ODevbase.DLL;
2. ODevbase.Lib;
3. ODevbase.h;

## 二、接口说明

### 2.1 接口列表

| 名称                       | 访问域 | 说明                                                         |
| -------------------------- | ------ | ------------------------------------------------------------ |
| **IpreInitDev**            | Public | 初始化相关组件及硬件模块的初始参数                           |
| **IgetDefaultCommConfig**  | Public | 获取串口通讯模块默认配置参数，同时将串口信息配置的ini配置文件中的配置返回； |
| **IinitComm**              | Public | 初始化串口                                                   |
| **IcloseComm**             | Public | 关闭串口                                                     |
| **ISendCommand**           | Public | 发送命令                                                     |
| **IwaitResult**            | Public | 等待数据接收                                                 |
| **ISendAndRecvData**       | Public | 发送并接收数据                                               |
| **VClearRecvBuf**          | Public | 清除接收缓冲区                                               |
| **IEnterExclusiveSend**    | Public | 端口独占式访问                                               |
| **VReleaseExclusive**      | Public | 解除端口独占式访问                                           |
| **iWriteLog**              | Public | 记录日志信息                                                 |
| **vOnDataReceived**        | Public | 异步方式接收来自串口的数据                                   |
| **VcallProcFun**           | Public | 调用回调函数                                                 |
| **vSetBreakAsynRecvFlag**  | Public | 设置异步接收结果上传方式                                     |
| **bGetBreakAsynRecvFlag**  | Public | 获取异步接收结果上传方式                                     |
| **IGetIntValue**           | Public | 取得整型键值的配置项值                                       |
| **iGetStringValue**        | Public | 取得字符型键值的配置项值                                     |
| **ISetIntValue**           | Public | 设置整型键值的配置项值                                       |
| **iSetStringValue**        | Public | 设置字符型键值的配置项值                                     |
| **iExtendCommFunc**        | Public | 扩展的通讯方法                                               |
| **vSetLogicalDevName**     | Public | 设置驱动的逻辑设备名                                         |
| **VSetProcFun**            | Public | 设置回调函数指针                                             |
| **vSetProcFun2**           | Public | 设置回调函数指针，带额外参数的函数原型                       |
| **iEnableRevEvent**        | Public | 设置通讯组件在收到数据时，是否主动以事件通知                 |
| **IGetErrorFlag**          | Public | 驱动设备错误标志                                             |
| **TGetErrorFlag**          | Public | 驱动设备错误标志结构体                                       |
| **VSetErrorFlag**          | Public | 设置驱动设备错误标志，一种是设置错误标志，一种是设置错误结构体 |
| **IGetVersion**            | Public | 取得设备驱动版本                                             |
| **iGetDevVersion**         | Public | 驱动设备硬件版本                                             |
| **IGetStatus**             | Public | 取设备状态<br />**[虚函数,需要子类重写其功能]**              |
| **Iinit**                  | Public | 初始化硬件<br />**[虚函数,需要子类重写其功能]**              |
| **Itest**                  | Public | 测试硬件<br />**[虚函数,需要子类重写其功能]**                |
| **iInitComm2**             | Public | 初始化通讯通道，支持USB                                      |
| **iGetDefaultCommConfig2** | Public | 获取通讯通道默认配置参数，支持USB                            |
| **vClearRecvBuf2**         | Public | 清除通道接收缓冲区，支持USB                                  |
| **iSendCommand2**          | Public | 发送命令，支持USB                                            |
| **iWaitResult2**           | Public | 等待数据接收,支持USB                                         |
| **iSendAndRecvData2**      | Public | 发送并接收数据，支持USB                                      |
| **iEnterExclusiveComm2**   | Public | 端口独占式访问，支持USB                                      |
| **iLeaveExclusiveComm2**   | Public | 解除端口独占式访问，支持USB                                  |

### 2.2 宏定义说明

> // 通用定义

**SUCCESS**      0   // 通用定义，表示成功
**FAIL**          1   // 通用定义，表示成功

> // 清除错误缓存区

**ERRORFLAG_CLEAR**       0   // 清除错误缓存区

> // 日志信息提示类别

**CATALOG_FATAL**          0   // 致命错误
**CATALOG_NORMAL**       1   // 一般信息
**GATALOG_NOTE**          2   // 提示信息

> // 日志信息类型

**MSG_GENERAL**           1   // 一般信息
**MSG_COMM_SEND**        2   // 发送数据
**MSG_COMM_REV**         3   // 接收数据

> // 错误信息

**E_DEVBASE_BASE**            900 // Devbase 宏定义基址
**E_DEVBASE_HOLD**           910 //错误阙值
**E_DEVBASE_COMMANAGER**   911 // commanger组件创建失败
**E_DEVBASE_CFGMANAGER**    912 // cfgmanager组件创建失败
**E_DEVBASE_LOGSERVER**          913 // logserver组件创建失败
**E_DEVBASE_COMMLOGINIT**       914 // commlog组件初始化失败
**E_DEVBASE_TRACELOGINIT**      915 // tracelog组件初始化失败
**E_DEVBASE_COMCFGFILE**          916 // 打开GRGDTATM_CommCfg.ini失败
**E_DEVBASE_COMID**              917 // 读取配置ComID失败
**E_DEVBASE_CommType**          918 // 读取配置CommType失败
**E_DEVBASE_COMBAUD**           919 // 读取配置ComBaud失败
**E_DEVBASE_COMBOARDPORT**     920 // 读取配置ComBoardPort失败
**E_DEVBASE_COMBOARDPORTBAUD**921 // 读取配置ComBoardPortBaud失败
**E_DEVBASE_DEVCOMMLOGID**     922 // 读取配置DevCommLogID失败
**E_DEVBASE_DEVTRACELOGID**     923 // 读取配置DevTraceLogID失败
**E_DEVBASE_INICFGFILENAME**     924 // 打开IniCfgFileName项配置文件失败
**E_DEVBASE_LOGICNAMEISNULL**  925 // 设备的逻辑设备名为空
**E_DEVBASE_USBSENDIPE**     926 // 读取配置SendPipe失败
**E_DEVBASE_USBRECVPIPE**    927 // 读取配置RecvPipe失败
**E_DEVBASE_USBDEVICEGUID**     928 // 读取配置DeviceGUID失败

### 2.3 结构体说明

#### **tDevReturn** 

```cpp
typedef struct {
	int		iLogicCode;		// 逻辑错误码,设备类
	int		iPhyCode;		// 物理错误码
	int		iHandle;		// 处理方法：0-不处理 1-初始化 2-重发命令
	int		iType;			// 错误类型：0-警告 1-严重
	char	acReturnCode[128];  	// 硬件返回信息(逻辑码:物理码(返回信息))
	char	acReserve [128];    	// 保留信息, 记录硬件实际返回的原始错误信息（只需错误部分），不需作任何转换
} tDevReturn;
```

#### **tCommCfg**

```cpp
struct tCommCfg
{
	tCommType eCommType;	
	tCommInfo unCommInfo;
	char *pcExtern;
};
```

- **eCommType**：当前通讯端口的类型，有以下取值：
  COMM_COM：串口
  COMM_COMBOARD：串口板
  COMM_USB：USB

- **unCommInfo**：相应通讯类型的配置参数，以下是对应关系：
  COMM_COM：配置参数是tComInfo结构
  COMM_COMBOARD：配置参数是tComBoardInfo结构
  COMM_USB：配置参数是tUSBInfo结构

- **pcExtern**：扩展参数。格式为一系列的"<Name>=<Value>"的字串,Name表示自定义的扩展参数的名，Value表示相应扩展参数的值，有多少个参数，就有多少个这样的名值对。

#### **tCommCfg2**

```cc
struct tCommCfg2
{
	tCommType eCommType;
	tCommInfo unCommInfo;
	char *pcExtern;
};
```

### 2.4 成员变量说明

| 名称                   | 类型        | 访问域 | 说明                                           |
| ---------------------- | ----------- | ------ | ---------------------------------------------- |
| **m_acLogicalDevName** | char   [32] | public | 设备逻辑标识名称，需要在子类构造函数中给其赋值 |
| **m_acVersion**        | char   [32] | public | 驱动版本信息，需要在子类构造函数中给其赋值     |
| **m_acDevVersion**     | char   [32] | public | 硬件版本信息，需要在子类构造函数中给其赋值     |

## 三、配置串口

### 3.1 注册表

> HKEY_LOCAL_MACHINE\SOFTWARE\GrgBanking\DevBaseDll\CARDREADER

### 3.2 ini 文件

> GRGDTATM_CommCfg.ini

#### 3.2.1 Dunite 配置项

```ini
CommType=【通讯类型，1-COM; 2-COMBOARD; 3-USB】
ComID=【PC串口】
ComBaud=【PC串口波特率】
ComBoardPort=【串口分配器端口号】
ComBoardPortBaud=【串口分配器端口号的波特率】

DeviceGUID=【USB设备接口的GUID】
SendPipes=【USB设备的发送管道编号，可以多个，以逗号分开】
RecvPipes=【USB设备的接收管道编号，可以多个，以逗号分开】
ExtendStr=【扩充设置串，可以为多个VID/PID，用“；”隔开】
ReserveUsb=【USB保留字段】

DEVCOMMLOGID=【用于指向日志管理组件的配置文件GrgLogServer.cfg的配置项。如果为空表示不记录日志】
DEVTRACELOGID=【用于指向日志管理组件的配置文件GrgLogServer.cfg的配置项。如果为空表示不记录日志】
IniCfgFileName=【设备的其他配置ini文档的相对路径和文件名，没有则保留为空】
CONNECTOR=【用于设置USB设备连接端口，无指定或指定为 * 则表示该设备可以连接在任意位置，如CONNECTOR = USB1，表示该设备只能连接在名为USB1的端口上】
```

```ini
[RECEIPTPRN] //凭条打印机
CommType =3
DeviceGUID ={77F49320-16EF-11d2-AD51-006097B514DD}
SendPipes =1,3
RecvPipes =0
ExtendStr =
ReserveUSB =
ComID =”1”
ComBaud =”9600”
ComBoardPort =”2”
ComBoardPortBaud =”9600”
DevCommLogID = “DACS_WOSA_SP COMM通讯日志”
DevTraceLogID =
IniCfgFileName = "指向另一配置文件，修改其中的 DevDriverLog 键和 RemainDays 键"
```

### 3.3 GrgLogServer.cfg

> Dunite安装目录 \ Bin \ GrgLogServer.cfg

| 配置项           | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| DebugMode        | 调试模式，1-调试模式，所有日志立即写入，不考虑日志信息是否缓冲写入，用于调试程序时实时看到日志信息; 0-非调试模式，可正常缓冲写入,默认0 |
| MaxFileSize      | 单个日志文件最大大小,以KB为单位，超过该大小则新建日志文件,该值范围为1024-10240,默认1024 |
| MaxQueueCount    | 缓冲队列数，如果缓冲记录时超过该记录条数则写入硬盘,该值范围为10-200,默认30 |
| FileName         | 日志文件名，不能够有下划线，最终的文件名会加上后缀(_001等)   |
| MaxRebuildDate   | 最长多少小时需要重建新的日志文件,以小时为单位,范围1-24小时,缺省24小时 |
| RebuildFileType  | 重建文件的类型，0-只考虑文件大小,不考虑文件建立的时间 1-只考虑文件建立的时间,不考虑文件大小 2-同时考虑文件建立的时间和文件大小,默认2 |
| FileCrypt        | 是否需要对整个日志文件内容加密，0-不加密,1-加密,默认0        |
| InfoCrypt        | 是否需要对日志信息中具体日志内容那个字段加密，0-不加密,1-加密,默认0 |
| DeviceFieldCrypt | 是否需要对日志信息中模块设备名那个字段加密，0-不加密,1-加密,默认0 |
| MaxFreeDisk      | 记录日志那个硬盘分区最大剩余空间，以兆为单位。磁盘分区空间低于该值则删除最旧的10个日志文件,该值范围为300-2048,默认300 |
| MaxLogLength     | 单条日志信息的内容最大长度，大于该值则自动截取,该值范围为300-10240字符(hrLog)/字节(hrLogVariant),默认300 |
| LogTraceLevel    | 根据日志记录的级别来决定记录什么日志，0-不记日志，直接返回,1-记录所有日志,2-只记错误信息,3-只记警告信息和提示信息,默认1 |
| LogDefault       | 当FileName非法或未指定,或者调用hrInitLog时指定的section未找到,是否记录缺省日志 1 记录 0不记,默认不记 |
| DefaultPath      | 缺省日志文件名，默认D:\GrgBanking\DTATMW\Log\log.txt         |
| RemainDays       | 日志保留天数                                                 |

```ini
;8240机芯Comm
[1120]            ;通信配置文件中DevCommLogID对应的值
MaxFileSize=5120
MaxQueueCount=10
FileName=C:\GrgBanking\LOG\hardware\1120.txt ;日志保存路径及文件名
MaxRebuildDate=24
FileCrypt=0
InfoCrypt=0
DeviceFieldCrypt=0
MaxFreeDisk=800
RebuildFileType=2
MaxLogLength=10000
LogTraceLevel=1       ;是否开启日志：1为开启，0为不记日志
```

## 四、备注

### 4.1 构造函数成员变量

```cpp
_tcscpy(m_acVersion, "V2.1.2 b6");
_tcscpy(m_acDevVersion, "V1.1");
_tcscpy(m_acLogicalDevName, "USERKEY");
```

### 4.2 串口设置

1. 定义及初始化变量 --> 初始化组件**[**iPreInitDev**]** --> 取得默认串口参数**[**iGetDefaultCommConfig**]** --> 设置相关串口配置(数据长度、停止位、奇偶效验) --> 初始化串口**[**iInitComm**]**；
2. 对于USB设备的步骤：定义及初始化变量 --> 初始化组件**[**iPreInitDev**]** --> 取得默认串口参数**[**iGetDefaultCommConfig2**]** --> 设置相关配置 --> 初始化通讯设备**[**iInitComm2**]**；
3. 流程图如下：

![img](E:\marktext\image cache\grg_设置串口流程.gif)

### 4.3 重置串口分配器参数

串口分配器由于现场干扰等原因可能会发生通讯设置参数丢失从而导致通讯故障，当某一设备检测到通讯故障时可以通过重置该设备对应的串口分配器子口参数来试图恢复通讯。重置过程的处理如下调用：

- 如果是设备动态库内部处理：

```
tCommCfg l_sCommCfg;
memset(&l_sCommCfg, 0, sizeof(tCommCfg));

this->iGetDefaultCommConfig(l_sCommCfg); 
//注意此时得到的l_sCommCfg参数即是上次已经通过iInitComm设置的值，//所以不必再做修改

int l_iResult = this->iInitComm(l_sCommCfg);
if (SUCCESS == l_iResult) //成功
{
      …
}
```

如果是上层程序试图重设参数，以Sankyo读卡器对象g_oDevSankyo为例：

```cpp
tCommCfg l_sCommCfg;
memset(&l_sCommCfg, 0, sizeof(tCommCfg));

g_oDevSankyo.iGetDefaultCommConfig(l_sCommCfg);
//注意此时得到的l_sCommCfg参数即是上次已经通过iSetCommPara设置的值，所以不必再做修改

int l_iResult = g_oDevSankyo.iInitComm(l_sCommCfg);
if (SUCCESS == l_iResult)     //成功
{
       …
}
```

### 4.4 异步接收

1. 继承并重写vOnDataReceived的函数内容，注意不能在重写vOnDataReceived时调用基类的同一方法，以避免两次调用回调处理函数。注意这一步并不是必需，当需要改变vOnDataReceived的基类缺省实现时才需要改写，基类缺省实现为调用客户设置的回调函数；
2. 使用时需调用iEnableRevEvent(true); 通知通讯组件以异步方式接收数据；
3. 使用结束后需调用iEnableRevEvent(false); 以同步方式结束数据；否则将会在调用iWaitResult时取不到数据；
4. 备注：2，3两步可以由应用程序进行，也可以由驱动进行，具体视使用情况来定。

### 4.5 异步发送

#### 4.5.1 对驱动

1. 定义回调类型，最好用宏定义的方式，值以E_***_BASE为基数向上计数增加；上层将根据此值对数据进行处理；
2. 形成传送数据；
3. 调用vCallProcFun();

```cpp
#define		E_KEY_BASE		1900

char		l_ acData[128];
vCallProcFun(E_KEY_BASE, l_acData，128，(const void*)m_pSinkObject);

char		l_ acData[128];
vCallProcFun(E_KEY_BASE+1, l_acData, 128， (const void*)m_pSinkObject);
```

#### 4.5.2 对上层

1. 编写回调函数 void vProcFun(int p_iType, WPARAM p_pParam, int p_iLen) 或者 void vProcFun2(int p_iType, WPARAM p_pParam, int p_iLen, const void* p_pvParam)；并在函数体类更改参数 p_iType 的不同值，做不同的处理；
2. 设置回调函数的地址给驱动；

```cc
	void (*l_vProFun)(int, WPARAM,int,);
	l_vProFun = vProcFun;
	g_pDev->vSetProcFun(l_vProFun);

或者
    
	void (*l_vProFun2)(int, WPARAM,int, const void*);
	l_vProFun2 = vProcFun2;
	g_pDev->vSetProcFun2(l_vProFun2, (void*)this);

// 注意vSetProcFun2的第二个参数可以是任意一个有效指针，做何种处理由 l_vProFun 指向的函数内部实现来决定
```

### 4.6 设置设备处理信息

1. 设备返回和错误结构体中的内容和前期版本有所不同；新增逻辑码和硬件返回；
2. 在设备出错时候需要调用vSetErrorFlag，将最新错误信息保存；
3. Odevbase v3.2.1 版本中的设备返回结构体和错误结构体已经统一，所以可以直接将接口中参数p_psrStatus赋值后设置给vSetErrorFlag的参数；如 vSetErrorFlag（p_psrStatus[0])；
