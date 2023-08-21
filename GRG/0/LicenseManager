# LicenseManager

- 用于进行软件 License 控制与管理的一套 C++ 静态库及相关实用程序。

## 组成

1. **LicManager**.lib：License **基本静态库**，提供了用于 License 管理与控制所需的核心函数；
2. **LicClient**.lib：License **编程客户端**，提供进行 license 控制所需的最基本的编程接口。需要实现 License 控制的各软件模块链接此静态库并调用其中的函数；
3. **LicenseGen**.exe：License **文件生成工具**，为一个 windows 下的可执行程序，用于根据各个参数生成 License 文件并保存到数据库以供历史查询；
4. **LicHostInfo**：**主机信息获取工具**，为一个命令行方式的可执行程序，同时提供 windows 与 linux 下两个版本，用于获取机器的网卡物理地址、CPUID、硬盘序列号、IP地址及主机名等信息。

## 使用

### LicManager

1. 在需要使用 LicManager 的工程的适当位置包含头文件 OLicManager.h，同时为工程增加链接库 LicManager.lib；
2. 实例化一个 OLicManager 对象；
3. 先调用 OLicManager 类的 iLInit 函数初始化 LicManager 库；
4. 再根据需要调用 LicManager 的其他功能函数；
5. 当不再使用 LicManager 时调用 iLFree 释放资源。

### LicClient

1. 在需要使用 LicClient 的工程的适当位置包含头文件 LicClient.h，同时为工程增加链接库 LicClient.lib；
2. 实例化一个 OLicClient 对象；
3. 根据功能需要调用相应的函数。

### LicenseGen

根据界面提示进行相应操作即可生成 license 文件。

### LicHostInfo

根据命令行提示获取程序运行机器上的硬件信息。

## 文件格式

- 由注释行、server 行、feature 行构成；
- 文件名缺省以 .lic 结尾；

### 注释行

由”#”符号开始的行被识别为注释行。

### SERVER 行

- Server 行用于标识一台特定的主机，license 管理器运行于该主机上，只有在网络 license 认证时才 license 文件才需要包含该行；

- Server 行的格式如下：   

  > SERVER <nodename> <HostID> <PortNumber>

  - 各个域含义如下：   
  - SERVER ：SERVER 行开始关键字，标识此行为 SERVER 行，不能更改;
  - <nodename>：主机名，  可以用户任意指定;
  - <HostID>：主机硬件标识，指定该服务器的硬件指纹信息，不能更改;
  - <PortNumber>：服务器的服务端口号，用户可以修改;
  - 一个server行的例子：    SERVER  rds-lwbin 001438120ac4 tcp:1800 。

### FEATURE 行    

- Feature 行用于对软件的功能进行限定，一个 license 文件可以包含多个 feature 行，以同时进行多个软件的功能 license 限定；

- 其格式如下：

  > FEATURE <FeatureName> <VendorName>  <Version> <Expiration-Date> <#UserCount> <Key> <HostID>  <Vendor-String>  

- 各个域含义如下：    
  - FEATURE：FEATURE 行开始关键字，标识些行为 FEATURE 行，不能更改；
  - <FeatureName>：软件功能名，license 授权的单位都是针对软件功能及其版本进行，一个 Feature 行由软件功能名及其版本号唯一标识，不得重复；
  - <Vendor-Name>：供应商标识号；
  - <Version>：软件功能版本号；
  - <Expiration-Date>：过期时间，格式为”yyyy-mmm-dd”，mmm 为月份英文名前三个字符。过期时间为“permanent”表示永不过期；
  - <#UserCount>：软件许可用户数，为整数值，如果设置为 0 表示用户数不受限制，这种情况 license 的认证在软件运行的本机进行，不需要 license 后台服务；
  - <Key>：License序列号，一个软件功能 FEATURE 行有一个；
  - <HostID>：主机硬件指纹信息，用于绑定运行主机。当为”any”时表示任意主机；
  - <Vendor-String>：厂商自定义串，为厂商扩展用，这是一个可选项。

- 一个绑定到 yuntong 机器、非用户限制、过期时间为 2006 年 12 月 31 日的 license 的例子：

> FEATURE Dacs-wosa GRG 1.0.0  2006-dec-31 0 1234567890abcde yuntong  magic   
>
> 这个 license 还带有一个厂商扩展串 magic。

综上格式描述，下面是两个 license 文件的例子：

1) 含两个软件功能（即两个 feature 行）多用户 license 的文件： 
   #sample license file 1 
   SERVER rds-lwbin 001438120ac4  tcp:1800 
   FEATURE dacs-wosa GRG 1.00  2006-dec-31 5 123456789abcdef any  magic 
   FEATURE dunite  GRG 1.00 2006-dec-31 5 134540289abcdef  yuntong  magic     
2) 含一个软件功能的单机锁定 license 文件：
   #sample license file 2,node locked to mac:001438120ac4 
   FEATURE f1 GRG 1.00 2006-dec-31 0 3802846283743e3  001438120ac4  “”

## 文件路径设定

- license 文件路径通过环境变量 GRG_LICENSE_FILE设定，多个 license 文件路径以 ”:” 隔开；

- 用户数限制模式的 license 文件位于网络 license 服务器端，客户端的 GRG_LICENSE_FILE 以 ”端口号@服务器地址” 的格式指定网络 license；

- 下面是包含两个 license 文件路径的 GRG_LICENSE_FILE 环境变量设定，一个为本地  license 文件，另一个 license 位置在 IP 为 172.20.1.197 的机器上通过端口 2800 提供 license 认证服务：

  > set  GRG_LICENSE_FILE = c:\grglicense\grglicense.lic:2800@172.20.1.197

## LicManager 接口

- LicManager 包含了 license 控制所需的所有核心函数，License 生成、信息获取工具以及客户端静态库的实现均是基于 LicManager 提供的基础 license 功能;
- LicManager 的接口均通过类 OLicManager 的成员函数方式提供，要调用 LicManager 的接口函数，首先需要实例化一个 OLicManager 对象，然后通过对象进行相应的函数调用。

### 加载与释放

#### iLInit

> int iLInit(LM_HANDLE&p_sHandle)

**功能**：初始化 LicManager 库；

**参数**：p_sHandle 
		   [输出] License 任务结构；

**返回**

- 1：成功；
- <1：失败，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码：

1. LM_NONETWORK
2. LM_BADSYSDATE

#### iLFree

> iLFree(LM HANDLE p sHandle)

**功能**：释放 license 库分配的相关资源，结束本次 license 任务。

**参数**：p_sHandle 
		   [输入] License 任务结构;

**返回**此函数目前总是返回 1 表示成功，因为其资源都是内部分配的，能够由自己释放。

#### iLLoadLicFile

> iLLoadLicFile(LM_HANDLE p_sHandle,const char*p_pcEnv)

**功能**：从环境变量指定的路径装载 license 文件。

**参数**：

1. psHandle 
   [输入] License 任务结构；
2. p_pcEnv 
   [输入] 环境变量名，缺省为 GrgLicenseFile，里边可以包括多个文件路径，由”：”分隔；

**返回**>0：成功，表示被成功装载的 license 文件个数；<0：装载错误，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT
3. LM_NOCONFFILE
4. LM_BADFILE

#### iLInstallEnv

> iLInstallEnv(LM_HANDLE p_sHandle,const char* p_peValue,int p_iAppend,const char* p_pcEnv)

**功能**：设置 license 文件路径到环境变量。

**参数**：

1. p_sHandle
   [输入] License 任务结构
2. p_pcValue
   [输入] License 文件路径
3. p_iAppend
   [输入] 是否在已有的值后边追加，多个值以”：”隔开。缺省为 1，表示为追加方式，否则替换以前的环境变量值。
4. p_pcEnv
   [输入] 环境变量名，缺省为 GrgLicenseFile。

**返回**1：成功；<1：失败，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT
3. LM_INTERNAL ERROR

### 软件功能许可

#### iLCheckout 检出

> iLCheckout(LM_HANDLE p_sHandle, const char* p_pcFeature, const char* p_peVersion, const int p_iLicNum, int p_iFlag)

**功能**：检出一个软件功能的 license；

**参数**：

1. p_sHandle
   [输入] License 任务结构
2. p_pcFeature
   [输入] 待检出的软件功能名
3. p_pcVersion
   [输入] 待检出的软件功能版本
4. p_iLicNum
   [输入] 需要输出的用户数，此参数只对网络 license 有意义
5. p_iFlag
   [输入] 保留值，目前没有用到

**返回**1：成功；<1：失败，通过pcErrorText可以得到返回值对应的错误描述信息。

本函数可能返回的错误码：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT
3. LM_NOFEATURE
4. LM_BADKEYDATA
5. LM_BAD_VERSION
6. LM_LONGGONE
7. LM_NOTTHISHOST
8. LM_MAXUSERS

#### iLCheckin 检入

> iLCheckin(LM HANDLE p_sHandle, const char* p_pcFeature, const char* p_peversion)

**功能**：检入一个软件功能的 license。

**参数**：

1. p_sHandle
   [输入] License 任务结构
2. p_pcFeature
   [输入] 待检入的软件功能名
3. p_pcVersion
   [输入] 待检出的软件功能版本

**返回**1: 成功；<1: 失败，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT
3. LM_NOFEATURE

#### psFindFeature 查询

> psFindFeature(LM HANDLE p sHandle,const char* p_pcFeatureName,const char* 
> p_pcFeatureVersion = NULL)

**功能**：取一个软件功能对应的license信息，信息以tConfig结构的形式返回。
**参数**：

- p_sHandle
  [输入]License任务结构
- p_pcFeatureName
  [输入]需要查找的软件功能名。
- p_pcFeatureVersion
  [输入]需要查找的软件功能版本，如果为NULL则可以是任意版本。

**返回**

- 成功返回指向 tConfig 结构的有效指针；
- 失败则返回 NULL，通过 iGetLibLastError 可以取得错误号。

本函数可能返回的错误码如下：

1. LM_BADPARAM
2. LM_NOFEATURE
3. LM_NOLICMANAGERINIT

#### iLGetLicField 指定域

> iLGetLicField(LM HANDLE p_sHandle,const char* p_pcFeatureName,const char* 
> p_pcVersion,eLicField p_eLicField,char* p_pcFieldStr,int* p_iFieldLen)

**功能**：取license信息的某一个域。
**参数**：

- p_sHandle
  [输入]License任务结构
- p_pcFeatureName
  [输入]待查找的软件功能名
- p_pcVersiion
  [输入]待查找的软件功能版本
- p_eLicField
  [输入]需要获取相应值的 license 域类型，根据 license 文件的格式，目前支持获取值的域类型有：

| License信息域    | 对应的枚举值 |
| ---------------- | ------------ |
| 软件功能名       | 0            |
| 厂商名           | 1            |
| 软件功能版本     | 2            |
| Licensei过期时间 | 3            |
| 许可用户数       | 4            |
| License key      | 5            |
| 主机硬件标识     | 6            |
| 厂商自定义串     | 7            |

- p_pcFieldStr
  [输出]对应license信息域的值。
- p_iFieldLen
  [输出]域信息的长度。

**返回**

- 1: 成功，p_pcFieldStr 包含对应软件功能 license 行指定域字符串；
- <1：失败，通过 pcErrorTexti 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码如下：

1. LM_BADPARAM
2. LM_NOFEATURE
3. LM_NOLICMANAGERINIT

### 信息解析

#### iLAllFeature 文件

> iLAllFeature(LM HANDLE p_sHandle,FILE* p_pfLicFile)

**功能**：解析一个文件里的所有 license 信息保存于 LicManager 的私有数据区。此成员函数一般用于内部使用。

**参数**：

1. p_sHandle
   [输入] License 任务结构
2. p_pfLicFile
   [输入] license 文件的文件指针

**返回**

- \>0：被成功解析出的软件功能 License 个数；
- 0：文件中没有有效的 license 信息，可能是注释或者其它无效信息。
- <0：失败，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT

#### iLParseFeatureLine  文件行

> iLParseFeatureLine(LM_HANDLE p_sHandle,char* p_psLine, tConfig* p_psConfig, int* p_piError)

**功能**：分析一个 license 文件行。
**参数**：

1. p_sHandle
   [输入] License 任务结构
2. p_psLine
   [输入] Licenset 信息串
3. p_psConfig
   [输出] license 信息结构，包含 license 信息的各个项
4. p_piError
   [输出] 错误码，成功时值为零，否则指示错误号。

**返回**：成功返回 1，p_psConfig 包含有效的解析信息，不成功返回 0，p_iError 指示错误号。

本函数可能返回的错误：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT

#### iLDecryptKey 解密

> iLDecryptKey(LM_HANDLE p_sHandle,unsigned char* p_pcKey,int p_piKeyLen, char* * pcMsg, int* p_piMsgLen)

**功能**：将一个 License key 还原成摘要码

**参数**：

- p_sHandle
  [输入] License 任务结构
- p_pcKey
  [输入] license key, 从 license 文件中可以得到。
- P_piKeyLen
  [输入] license key 的长度。
- pcMsg
  [输出] 摘要字符串，调用都需要负责释放（通过 delete[ ] 操作）。
- p_piMsgLen
  [输出] 摘要串的长度。

**返回**

- 1：成功，pcMsg 包含有效的摘要信息，p_piMsgLen 指向有效的摘要长度；
- <0：失败，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误如下：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT
3. LM_INTERNAL_ERROR

#### pcFeatureString 结构

> pcFeatureString(LM_HANDLE p_sHandle,const tConfig* ,int p_iForKeyGen = 1, int p_iLine = FEATURE LINE)

**功能**：由一个 license 信息结构 tConfig 得到对应的 license 信息串。

**参数**：

- p_sHandle
  [输入] License 任务结构
- p_iForKeyGen
  [输入] 标志变量，指定生成的功能串是否用于再产生 license key，如果用于生产 license key 则得到的串里不能再包含 key 的值。缺省值为 1，即返回的串不能包括 license key。
- p_iLine
  [输入] 需要获得的信息串类型。按 license 文件的格式，由三种信息行组成：1）SERVER 行，指定 license 服务器的信息，2) DAEMON 行，指定后台服务进程的信息，3) FEATURE 行，指定软件功能授权的信息。缺省值为 FEATURE 行。目前暂不支持网络 licensel 服务，所以 SERVER 行与 DAEMON 行均无意义。

**返回**：函数成功返回 license 串，失败则返回 NULL。通过 iGetLibLastError 可能得到操作的出错号。

本函数可能返回的错误如下：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT
3. LM_NOTSUPPORTED

#### iLCryptStr 加密

> iLCryptStr(LM HANDLE p_sHandle, char* p_pcMsg, char* * p_pcKey, int* p_piKeyLen)

**功能**：加密一个字符串。

**参数**：

- p_sHandle
  [输入]License任务结构
- p_pcMsg
  [输入]待加密的字符串
- p_pcKey
  [输出]输出的密文，需要由调用者进行释放（通过delete[ ] 操作）。
- p_pikeyLen
  [输出]输出的密文长度。

**返回**

- 1：成功；
- <1：失败，通过ocErrorText可以得到返回值对应的错误描述信息。

本函数可能返回的错误码如下：

1. LM_BADPARAM
2. LM_INTERNAL_ERROR

#### iLCryptStr 生成密钥

> iLCryptStr(LM_HANDLE p_sHandle,const tConfig&p_sConfig,char* * p_pcKey,int* 
> p_piKeyLen)

**功能**：从 tConfig 信息结构中生成 license key

**参数**：

- p sHandle
  [输入]License任务结构
- p_sConfig
  [输入]一个软件功能的 license 信息结构
- p_pcKey
  [输出]生成的license key.
- p piKeyLen
  [输出]生成的license key的长度。

**返回**

- 1：成功；
- <1：失败，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码如下：

1. LM_BADPARAM
2. LM_INTERNAL_ERROR

#### iLMsgDigest 生成摘要

> iLMsgDigest(LM_HANDLE p_sHandle,charp_peMsg,unsigned char* * p_pcDigest,int* p_piDigestLen)

**功能**：为一个字符串生成摘要。

**参数**：

- p_sHandle
  [输入]Licenset任务结构
- p_pcMsg
  [输入]待生成摘要的消息
- p_pcDigest
  [输出]生成的摘要，调用者需要负责释放（通过delete[ ]）。
- p piDigestLen
  [输出]生成的摘要长度。

**返回**

- 1：成功，p_pcDigest包含生成的摘要码；
- <1：失败，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码如下：

1. LM_BADPARAM
2. LM_INTERNAL_ERROR
3. LM_NOLICMANAGERINIT

### 主机检测

#### iLDate 过期

> iLDate(LM_HANDLE p_sHandle,char* p_pcDate, int p_iFlag)

**功能**：判断传入的日期跟当前系统时间比较是否过期。

**参数**：

- p_sHandle
  [输入] License 任务结构
- p_pcDate
  [输入] 日期字符串
- p_iFlag
  [输入] 处理标志，保留用，目前不起作用。

**返回**

- 1：没有过期；0：己经过期，<0：调用错误；

本函数可能的错误如下：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT

#### iLHost 主机

> iLHost(LM_HANDLE p_sHandle, tHostID* p_psHostID)

**功能**：判断主机 ID 是否与程序运行本机是否相符。

**参数**：

- p_sHandle
  [输入] License任务结构
- p_psHostID
  [输入] 主机标识

**返回**

- 1：参数ID与本机ID相符：0：不相符。<0：调用错误；

本函数可能的错误如下：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT

#### iLVLK 有效校验

> iLVLK(LM_HANDLE p_sHandle,tConfig* p_psConfig)

**功能**：较验 license key 是否有效，license key 保存在信息结构 tConfig 的 acKey 成员中。

**参数**：

- p_sHandle
  [输入] License 任务结构
- p_psConfig
  [输入] 软件功能的 license 信息结构，从 license 文件是解析而得，包含该软件功能的
   license 授权信息。

**返回**

- 1：成功，表示 p_psConfig 所指的 license key 有效；
- 0：无效 license key；
- <0：调用错误，本函数可能的错误码如下：
  1. LMBADPARAM
  2. LM_NOLICMANAGERINIT

#### iLGetHostID 硬件信息

> iLGetHostID(LM HANDLE p sHandle,int p_ilDType,tHostID* p_psHostID,int p_iSuffix = 1)

**功能**：获得主机的硬件指纹信息。

**参数**：

- psHandle
  [输入] License 任务结构
- piIDType
  [输入] 需要获得的主机指纹类型，目前支持以下几种类型：
  1. 如果为 0 则表示任意的主机标识；
  2. 以太网卡物理地址，标识值为 1；
  3. 主机计算机名，标识值为 2；
  4. 磁盘序列号，标识值为 4；
  5. CPU 序列号，标识值为 8；
  6. 网卡绑定的 IP 地址，标识值为 16；

- p_psHostID
  [输出] 主机 ID 结构，保存获得的主机硬件标识及相关类型。

- p_iSuffix
  [输入] 指定获取的主机标识信息是否包括类型信息，类型信息由最后一个字符表示。后
  缀字符含义如下：

  | 硬件指纹类型     | 类型后缀字符 |
  | ---------------- | ------------ |
  | 以太网卡物理地址 | a            |
  | 主机计算机名     | b            |
  | 磁盘序列号       | c            |
  | CPU 序列号       | d            |
  | IP 地址          | e            |

**返回**

- 1：成功，p_psHostID 指向有效的主机硬件标识信息；
- <1：获取硬件信息失败，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码如下：

1. LM_BADPARAM
2. LM_NOLICMANAGERINIT
3. LM_INTERNAL_ERROR
4. LM_NOTSUPPORTED

#### iLCatHostID 字符转换

> iLCatHostID(LM_HANDLE p_sHandle,const char* p_pelD,int p_iType,tHostID& 	p_sHostID)

**功能**：将一个字符串转换成 tHostID 结构，如果 tHostID 己有 ID 信息存在，则在原来的基础上追加。

**参数**：

- p_sHandle
  [输入] License 任务结构
- p_pcID
  [输入] ID 字符串
- p_iType
  [输入] ID 类型信息，为 'a' - ‘e' 间的字符，相应含义见 iLGetHostID 的参数说明。
- p_sHostID
  [输出] 返回给调用者的主机 ID 结构。

**返回**

- 1：成功；
- <1：失败，通过ocErrorText可以得到返回值对应的错误描述信息。

本函数可能返回的错误码如下：

- LM_BADPARAM

#### iLHostIDType 标识类型

> iLHostIDType(LM_HANDLE p_sHandle,const char* p_pclD,vector<char*>*p_pvecIDs,int& p_iType)

**功能**：获得一个主机标识串的类型，并将其解析到一个数组。该主机标识串可以包含多个硬件指纹信息，由 ”+” 联接。

**参数**：

- p_sHandle
  [输入] License 任务结构
- p_peID
  [输入] p_pcID 主机标识字符串。
- p_pvecIDs
  [输出] 返回给调用者的硬件指纹数组，该数组里的项是由 p_pcID 解析而得到的，如果传
  入指针为空表示调用者不需要具体的硬件指纹内容。
- P_iType
  [输出] p_pcID 的标识串里的硬件信息类型，类型信息见 iLGetHostIDi 函数的 p_iIDType 参数说明。

**返回**

- \>1：成功，表示有多少个不同类别的硬件标识被解析；
- 0：表示没有可解析硬件标识食信息：
- <1：调用失败，通过 pcErrorText 可以得到返回值对应的错误描述信息。

本函数可能返回的错误码如下：

1. LM_BADPARAM

#### iLCompareVersion

> iLCompareVersion(LM_HANDLE p_sHandle,const char* p_pcVer1,const char* p_peVer2)

**功能**：比较版本号。
**参数**：

- p_sHandle
  [输入]License任务结构
- p_pcVerl
  [输入]基准版本
- p_pcVer2
  [输入]待比较的版本

**返回**：如果o_pcVerl >= p_pcVer2 返回1，否则返回 0。

### 服务器

#### iLHeartBeat 是否服务

> iLHeartBeat(LM_HANDLE p_sHandle,const char* p_pcServerName)

**功能**：检测一个license服务器是否在线服务。
**参数**：

- p_sHandle
  [输入]License任务结构
- p_pcServerName
  [输入]服务器名

**返回**：

- 1：成功；
- <1：失败，通过pcErrorText可以得到返回值对应的错误描述信息。

**备注**：目前尚不支持网络1 icense,所以此方法暂没有意义。

#### iLUserList 使用列表

> iLUserList(LM_HANDLE p_sHandle,char*p_pcFeature,tLMUsers**p_ppsUsers)

**功能**：从license 服务上获取一个软件功能的在用用户列表。
**参数**：

- p_sHandle
  [输入]License任务结构
- p_pcFeature
  [输入]软件功能名
- p_ppsUsers
  [输出]用户列表

**返回**：

- 1：成功；
- <1：失败，通过pcErrorText可以得到返回值对应的错误描述信息。
  备注：目前尚不支持网络 license，所以此方法暂没有意义。

#### pcErrorText 错误描述

> pcErrorText(int p_iErrorCode)

**功能**：由一个错误号取得其对应的错误描述信息。
**参数**：

- p_iErrorCode
  [输入]错误号

**返回**：错误描述信息，如果没有错误号对应的错误描述信息则返回空串。

#### iGetLibLastError  错误号

> iGetLibLastError(void)

**功能**：取 LicManager 库函数操作最后一次的错误号。
参数：无
**返回**：错误编码。

## LicClient 接口

1. LicClient 模块提供 **LicClient.lib** 与 **LibClient.h** 两个文件供需要进行 license 较验的软件模块使用，以**实现软件 license 的控制与信息查询**功能；
2. LicClient 的接口函数均通过类 OLicClient 的成员函数方式提供，要调用 LicClient 的接口函数，**首先**需要**实例化一个 OLicManager 对象**，然后通过对象进行相应的函数调用。

### iLcCheckout

> int iLcCheckout(const char* p_pcFeatureName,const char* 
> p_pcFeatureVersion, int p iUserCount)

功能：检出一个软件功能的1 icense。
参数：

- p_pcFeatureName
  [输入]软件功能名
- p_pcFeatureVersion
  [输入]软件功能版本
- p_iUserCount
  [输入]需要检出的用户数，此参数只对网络多用户 license 有意义。目前没有支持。

返回：

- 1：成功；
- <1：失败，通过pcLcErrorString函数可以得到返回值对应的错误描述信息。

### iLcCheckin

> int iLcCheckin(const char* p_pcFeatureName,const char* p_pcFeatureVersion)

功能：检入一个软件功能的1 icense.。当软件不需1 icense运行许可时便将 license 进行检入。
参数：

- p_pcFeatureName
  [输入]软件功能名
- p_pcFeatureversion
  [输入]软件功能版本

返回：

- 1：成功；
- <1：失败，通过pcLcErrorString函数可以得到返回值对应的错误描述信息。

### iLcGetField

> int iLcGetField(const char* p_pcFeatureName, const char* 
> p_pcFeatureVersion,eLicenseField p_eLicField,char* p_pcOutvalue, int& p_iOutLen)

功能：取一个软件功能 license 信息的相关域。
参数：

- p_pcFeatureName
  [输入]软件功能名
- p_pcFeatureVersion
  [输入]软件功能版本
- p_eLicField
  [输入]待获取的1 icense信息域类型
- p_pcOutValue
  [输出]输出的对应的域的值
- p_iOutLen
  [输入/输出]输出域值的长度

返回：

- 1：成功；
- <1：失败，通过pcLcErrorStringi函数可以得到返回值对应的错误描述信息。

备注：

- license信息域类型对应的着license文件的Feature行（即以Feature开头的软件功能 license 授权信息行)。
- 目前有以下必种域的值可以由 iLcGetField 函数获得：

| License信息域    | 对应的枚举值 |
| ---------------- | ------------ |
| 软件功能名       | 0            |
| 厂商名           | 1            |
| 软件功能版本     | 2            |
| Licensei过期时间 | 3            |
| 许可用户数       | 4            |
| License key      | 5            |
| 主机硬件标识     | 6            |
| 厂商自定义串     | 7            |

### pcLcErrorString

> char* pcLcErrorString(int p iErrorCode)

功能：取错误码对应的描述信息。
参数：

- p_iErrorCode
  [输入]错误码编号

返回：

- 如果存在错误码对应的描述信息则返回该描述串，否则返回ULL。

### iLcInstallLicPath

> int iLcInstallLicPath(const char* p_pcFilePath,int p_iAppend 1,const char* p_pcEnvVar = ENV_LICENSEPATH)

功能：设置 license 文件路径到系统环境变量，一次调用只能安装一个 license文件。
参数：

- p_pcFilePath
  [输入]License 文件路径
- p_iAppend
  [输入]是否为**追加**方式，**默认为是**，即将值追加到现有环境变量值的后边，**以”；”隔开**；**为 0 时为替换方式**，将现有值进行替换。
- p_pcEnvVar
  [输入]要设定的环境变量名，缺省环境变量是 GrgLicensePath.

返回：

- 1：成功；
- <1：失败，通过pcLcErrorString函数可以得到返回值对应的错误描述信息。

## 错误码

| 错误码               | 错误号 | 描述                                                         | 处理                                                         |
| -------------------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| LM_NOERROR           | 0      | 无错误                                                       |                                                              |
| LM_NOCONFFILE        | -1     | Cann’t find license file                                     | 检查系统环境变量GrgLicenseFile里所配置的License文件路径是否正确，如果不指定全路径则为当前运行程序的路径 |
| LM_BADFILE           | -2     | License file corrupted<br />License文件被非法改动。          | 用从供应商处获得的license文件替换被修改的文件，或者重新从供应商处获得license文件 |
| LM_NOSERVER          | -3     | Cannot connect to a license server.                          | 检查系统环境变量GrgLicenseFile里所配置的服务IP地址以及license文件里的Server行的端口参数与协议类型是否正确 |
| LM_MAXUSERS          | -4     | Maximum number of users reached.<br />达到最大用户数         | 等待其它用户释放占用的license。                              |
| LM_NOFEATURE         | -5     | No such feature exists.<br />不存在的软件功能名              | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确 |
| LM_BADCODE           | -8     | Bad encryption code                                          | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确 |
| LM_NOTTHISHOST       | -9     | Hostid doesn't match license.  <br />license的主机硬件标识与软件运行主机的硬件标识不一致。 | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确 |
| LM_LONGGONE          | -10    | Software Expired.<br />软件时间过期。                        | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确 |
| LM_BADDATE           | -11    | Bad date in license file.<br />License文件里的过期时间格式不对 | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确 |
| LM_NO_SERVER_IN_FILE | -13    | License文件没有指定license服务器                             | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确。这种情况只会在网络（即多用户）license的情况下才发生 |
| LM_BADHOST           | -14    | Bad SERVER hostname in license file.                         | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确 |
| LM_OLDVER            | -21    | 不支持的License文件格式(版本)。                              | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确 |
| LM_CHECKINBAD        | -22    | 检入软件功能失败                                             | 一般发生在网络license的情况下，需要检查服务器是否正常服务以及网络是否连通 |
| LM_CANTFINDETHER     | -29    | 找不到网卡                                                   | 检查硬件配置                                                 |
| LM_NOREADLIC         | -30    | 不能读取license文件                                          | 检查license文件是否被破坏                                    |
| LM_TOOEARLY          | -31    | 软件运行时间未到license允许的开始时间                        | 在license许可的运行期间内运行软件                            |
| LM_BADPARAM          | -42    | 错误的函数参数                                               | 检查传给License库函数的参数是否正确。                        |
| LM_NOKEYDATA         | -43    | License文件没有license key                                   | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确 |
| LM_BADKEYDATA        | -44    | 错误的License key                                            | 检查License文件是否正确，检查环境变量GrgLicenseFile的License文件设置是否正确 |
| LM_NOLICMANAGERINIT  | -51    | 没有初始化LicManager库                                       | 在需要使用LicManager时首先调用iLInit函数，后再进行其它的LicManager的库函数调用 |
| LM_NONETWORK         | -62    | 网络不能初始化                                               | 检查操作系统的网络模块版本                                   |
| LM_INTERNAL_ERROR    | -76    | LicManager内部错误                                           | 这个错误一般指示调用系统API失败或者license控制的一般性失败,一般不需针对其进行相应处理。要处理时需报告软件供应者。 |
| LM_BAD_VERSION       | -77    | 无效的软件功能版本                                           | 检查传给LicManager库函数相关的版本参数，并且确认license文件中有该版本对应的软件功能存在。版本格式目前只能是#.#.#，#号表示一位或者多位数字。 |
| LM_BADSYSDATE        | -88    | 系统时间被修改到正确时间之前的时间                           | 检查系统时间是否正确。                                       |
| LM_NOTSUPPORTED      | -133   | 不支持的操作                                                 | 内部错误，请联系软件供应者                                   |

