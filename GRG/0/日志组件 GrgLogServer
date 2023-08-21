# GrgLogServer

- 是一个COM组件；
- 当前系统的上层应用程序记录 LOG 信息时，不直接与硬盘上的 LOG 文件进行交互，而是通过日志组件 GrgLogServer 对磁盘文件进行操作来记日志。日志组件相当于应用程序与磁盘日志文件之间的中间层。

## 主要功能

- 提供了队列缓冲机制。应用只需要把本次的日志内容直接放入队列即可返回，不需要等待日志写入磁盘文件，这样就不影响应用的执行效率；
- 日志组件使用多线程，充分利用了CPU的资源。 

## 使用说明

1. 注册：当**安装 Dunite** 时，CommManager 会自动注册，如果要手工注册，请在命令行输入 Path\CommManager.exe  /regserver，Path 为 CommManager 所在的目录；
2. 在程序中包含 **CommManager.h 和 CommManager_i.c** 两个文件；
3. 在程序中创建 CommManager 组件实例，就可以使用。

## 使用步骤

1. 程序中**包含 LogServer.h 和 LogServer_i.c 两个文件**； 
2. 在使用 GrgLogServer 的线程中调用 **CoInitializeEx** **初始化** COM 库；
3. 调用 **CoCreateInstance** **创建** GrgLogServer 实例；
4. 调用 GrgLogServer 实例的接口 **hrInitLog** **初始化 GrgLogServer**，来与 GrgLogServer 建立一个新的连接；
5. 调用 **hrLog** 或者 **hrLogVariant** 接口方法进行**记录日志**的操作；
6. 调用 GrgLogServer 实例的 **hrCloseLog** 方法**与 GrgLogServer 断开连接**，不再使用GrgLogServer实例；
7. 调用每个 COM 组件的 Release 方法释放第 3 步初始化的 COM 组件实例；
8. 调用 **CoUnInitialize** **反初始化** COM 库。

## 配置文件 GrgLogServer.cfg

每个 session 表示一个配置 ID 号，当**调用 hrInitLog 时需要传入一个 ID 号参数**，日志组件会根据该 ID 号在配置文件中找到对应的配置信息，**如果没有找到配置 ID 则不记录日志**，ID 号**不区分大小写**。

### DebugMode

> 调试模式。
> **1 - 调试模式**，所有日志立即写入，不考虑日志信息是否缓冲写入，用于调试程序 时实时看到日志信息；**0 - 非调试模式**，可正常缓冲写入，**默认 0**。

### MaxFileSize 

> 单个日志文件的最大大小（以1K为单位）。 

一个日志文件**最大为 10 M**，应用设置参数时以 1K 为单位，如果指定的大小大于 10M(1024* 10K)，则日志文件大小定为 10M。 一个日志文件最小为 1M。如果指定的大小小于 1M，则日志文件大小定为 1M。 **默认值为 1M(1024*1K)**。

### MaxQueueCount 

> 缓冲队列的最大存储条数。

**最大为 200 条**。应用如果指定的大小大于 200 条，则缓冲队列的最大存储条数为 200 条，同时在系统日志里面把该操作记录下来。 **最小为 10 条**。应用如果指定的大小小于 10 条，则缓冲队列的最小存储条数为 10 条。 **默认值为 30 条**。 

### FileName 

> 日志文件名（**绝对路径**）。
> 配置文件中路径文件名中不能有下划线，生成的日志文件名称为指定名称后加入_001、_002...这样的标识。

### MaxRebuildDate 

> 每隔多长时间要重新建一个日志文件（以小时为单位）。

如果在记日志的过程中发现日志文件从建立到目前已经超过了指定的时间，就需要新建一个日志文件来继续记录日志。该指定的时间是**以小时为单位**，**默认是一天要新建一个**日志文件。如果到了 0 时，则会对于每一个现有日志文件都新建一个日志文件，**保证不是同一天的日志信息一定在不同的日志文件中**。如果 **RebuildFileType 为 0，则该项不起作用**，该项**默认值是 24**。

### RebuildFileType

> 重建文件的类型。
> **0 - 只考虑文件大小**，不考虑文件建立的时间；**1 - 只考虑文件建立的时间**，不考虑文件大小；**2 - 同时考虑文件建立的时间和文件大小**，**默认 2**。

### FileCrypt 

> 整个日志文件是否加密。
> **0 - 不加密 1 - 加密**。

### InfoCrypt

> 日志文件中一条记录里面的“信息”字段是否加密。
> **0 - 不加密 1 - 加密。**

### DeviceFieldCrypt

> 是否需要对日志信息中模块设备名那个字段加密。
> **0 - 不加密，1 - 加密，默认 0。**

### MaxFreeDisk

> 日志文件所在硬盘分区的大小。 

如果硬盘空间过小，不能新建日志文件记日志，需要删除该分区上老的 TXT 日志文件，然后再新建一个日志文件。同时要在系统日志文件里记录下硬盘空间不足的情况。该值**范围为 300 - 2048，默认保留空间是 300M**。 

### LogTraceLevel

> 记录日志的级别。 
> **为 0 则不进行日志记录；为 1 则记录所有日志信息；为 2 则只记录错误信息；为 3 则记录一般和警告信息，默认值 1。**

### MaxLogLength

单条日志信息的内容最大长度，大于该值则自动截取，该值范围为 300-10240 字符(hrLog)/字节(hrLogVariant)，**默认300**。

### LogDefault

当 FileName 非法或未指定，或者调用 hrInitLog 时指定的 section 未找到，是否记录缺省日志。**1 - 记录；0 - 不记，默认不记**。

### DefaultPath

缺省日志文件名，**默认 d:\log\log.txt**。

## 日志接口

### hrInitLog

- 与日志组件建立一个连接；
- 应用程序在使用日志组件时，要先调用hrInitLog确保与日志组件建立一个连接。连接建立成功后应用程序才能够通过hrLog或hrLogVariant记录日志信息。最多只能同时32个不同的模块写日志。

```cpp
HRESULT hrInitLog([in]int iID, [in]BSTR bstrDevice);
```

- **iID**：为配置文件 GrgLogConfig.cfg（内容类似于INI文件）中一个section。日志组件根据 iID 在配置文件中找到对应的配置项，如果没有找到则根据配置文件中的 [option] 段决定是否记录缺省日志；

- **bstrDevice**：应用程序指定是哪一个模块调用的日志记录。该字段将在最终的日志文件中"模块名"字段记录下来；

- > **返回值**：
  >
  > - S_OK：初始化日志组件成功，应用与日志组件建立了一个连接；
  > - S_FALSE: 初始化日志组件失败

### hrLog

- 记录日志到磁盘文件中；
- 如果日志信息中有字符串结束符'\0'时，要使用接口hrLogVariant来记录日志，否则将被截断。

```cpp
HRESULT hrLog([in]BSTR p_bstrData, [in]int p_iPriority, [in]int p_iInfoCatalog);
```

#### hrLogRaw

记录日志到磁盘文件中,不附加任何前缀信息。 

```cpp
HRESULT hrLogRaw([in]BSTR p_bstrData, [in]int p_iPriority, [in]int p_iInfoCatalog);
```

- **p_bstrData**：为应用程序这一次调用写日志时要记录的内容；

- **p_iPriority**：为应用程序这一次调用时的优先级。如果为 0，就要将缓冲队列中的日志信息立即写入硬盘。默认为 1，就是不立即写入硬盘，先进入缓冲队列。如果应用传入的该参数不是 0 或者 1，则默认为是 1；

- **iInfoCatalog**：为日志信息类别。0 - 致命错误；1 - 般信息；2 - 警告信息；

- > **返回值**：S_OK：记日志成功；S_FALSE：记日志失败。

### hrLogVariant

以字节流方式记录日志到磁盘文件中。  

```cpp
HRESULT hrLogVariant([in]VARIANT p_varData, [in]int iPriority, [in]int iInfoCatalog,[in,defaultvalue(0)]int  p_iLen);
```

#### hrLogVariantRaw

以字节流方式记录日志到磁盘文件中,不附加任何前缀。

```
HRESULT hrLogVariantRaw([in]VARIANT p_varData, [in]int iPriority, 
[in]int iInfoCatalog,[in,defaultvalue(0)]int p_iLen);
```

- **p_varData**：为应用程序这一次调用写日志时要记录的内容，是一个 VARIANT 类型的值。优点是可以在 VB 和脚本语言中调用该日志的 COM 组件，但是使用稍复杂；

- **iPriority**：为应用程序这一次调用时的优先级。如果为 0，就要将缓冲队列中的日志信息立即写入硬盘。默认为 1，就是不立即写入硬盘，先进入缓冲队列。如果应用传入的该参数不是 0 或者 1，则默认为是 1；

- **iInfoCatalog**：为日志信息类别。0 - 致命错误；1 - 般信息；2 - 警告信息；

- **p_iLen**：保留参数。目前不使用；

- > **返回值**：S_OK：记日志成功；S_FALSE：记日志失败。

### hrWriteAllLog

将日志COM组件中的所有缓冲队列信息写入硬盘。 

```cpp
HRESULT hrWriteAllLog();
```

- > **返回值**：S_OK：记日志成功；S_FALSE：记日志失败。

### hrCloseLog

断开应用与日志组件的连接。

```cpp
HRESULT hrCloseLog();
```

- **返回值**：S_OK：记日志成功；S_FALSE：记日志失败。

- > 当应用程序调用该函数时，GrgLogServer 组件会将本次连接的所有在缓冲队列中的日志信息立即写入硬盘。
