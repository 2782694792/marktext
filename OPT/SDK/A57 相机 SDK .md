# SDK 开发

## 1.2 SDK 目录

### 1.2.1 Windows 版

从 Ver2.1.0 版本开始，支持自定义安装路径。安装目录结构如下：

| 文件夹             | 描述                                                       |
|:--------------- |:-------------------------------------------------------- |
| Application     | 1. 相机客户端软件（MV Viewer），包含 32 位和 64 位；                     |
|                 | 2. 相机配套工具软件（CamTools），包含 32 位和 64 位。                     |
| Development     | 存放了 SDK 二次开发所需的各种资源文件；                                   |
|                 | 1. SDK 头文件、Lib 库；                                        |
|                 | 2. dotNet SDK 库；                                         |
|                 | 3. 第三方算法平台（Halcon、Sherlock）插件库；                          |
|                 | 4. SDK 例程（C、C++ Builder、C#、VB.Net、QT、Delphi、VB6、Python）。 |
| Documentantions | SDK 的各种开发文档（SDK 开发手册、API Doc 等）。                         |
| Drivers         | 1. GigE 驱动安装、卸载程序；                                       |
|                 | 2. U3V 驱动安装、卸载程序；                                        |
|                 | 3. x86 智能相机驱动安装、卸载程序；                                    |
|                 | 4. DShowFilter 安装、卸载程序。                                  |
| Runtime         | SDK 运行时库（包含 GenICam V3.0 第三方库），包含 32 位和 64 位             |

## 1.3 SDK 资源（C 接口）

| 大类别         | 小类别         | 文件位置                                                                               |
| ----------- | ----------- | ---------------------------------------------------------------------------------- |
| Application | 头文件         | .\Development\Include\GenICam\CAPI\SDK.h                                           |
|             | Lib 库（32 位） | .\Development\Lib\win32\MVSDKmd.lib                                                |
|             | Lib 库（64 位） | .\Development\Lib\x64\MVSDKmd.lib                                                  |
|             | 运行库（32 位）   | .\Runtime\Win32\MVSDKmd.dll                                                        |
|             | 运行库（64 位）   | .\Runtime\x64\MVSDKmd.dll                                                          |
|             |             |                                                                                    |
| 转码库         | 头文件         | .\ImageConvert\Include\ImageConvert.h                                              |
|             | Lib 库（32 位） | .\ImageConvert\Lib\win32\vs2013shared\ImageConvert.lib                             |
|             | Lib 库（64 位） | .\ImageConvert\Lib\x64\vs2013shared\ImageConvert.lib                               |
|             | 运行库（32 位）   | .\ImageConvert\Lib\x64\vs2013shared\ImageConvert.lib                               |
|             | 运行库（64 位）   | .\ImageConvert\Dll \x64\ImageConvert.dll                                           |
| 开发文档        | 中文          | .\Documentations\GenICam_API_C_CHS.chm<br/>.\Documentations\工业相机 SDK 开发手册(C 篇).pdf |
|             | 英文          | .\Documentations\GenICam_API_C_ENG.chm                                             |

## 1.4 SDK 开发工程配置

### 1.4.2 VS2019

以 Debug | Win32 为例，其它组合参考该配置。

（1）拷贝 SDK 相关资源文件

1. 拷贝头文件
   
   <img title="" src="../../image cache/2022-09-22-19-01-51-image.png" alt="" data-align="center">

2. 拷贝 Lib 库
   
   <img src="../../image cache/2022-09-22-09-06-37-image.png" title="" alt="" data-align="center">

3. 拷贝运行时动态库
   
   不需要拷贝 DLL 库。在安装 SDK 的环境里，运行程序时会自动通过 PATH 环境变量找到“【SDK 安装目录】\Runtime”路径下，对应平台的运行时动态库。

（2）配置工程

1. 配置头文件
   
   <img title="" src="../../image cache/2022-09-22-18-58-20-image.png" alt="" data-align="center">

2. 配置 Lib 库

<img src="../../image cache/2022-09-22-19-13-14-image.png" title="" alt="" data-align="center">

![](D:\Download\MarkText\workspace\image\2022-10-08-11-42-41-image.png)

# 2、SDK 整体调用流程

<img title="" src="../../image cache/2022-09-22-19-42-41-image.png" alt="" data-align="center">

# 3、SDK 各功能接口调用说明

- SDK 接口头文件：GenICam/CAPI/SDK.h

- 转码库接口头文件：ImageConvert.h

## 3.1 生成系统单例

接口：

```cpp
int32_t GENICAM_getSystemInstance(GENICAM_System ** ppSystem);
```

功能说明：

    创建系统单例对象，全局唯一。执行成功返回值为 0，失败返回值小于 0；

    [out] ppSystem 系统单例指针。

示例：

> GENICAM_System * pSystem = NULL;
> int32_t ret = GENICAM_getSystemInstance( &pSystem );

## 3.2 设备发现

接口：

```cpp
int32_t (*discovery)(struct GENICAM_System *thiz
        , GENICAM_Camera **ppCameraList
        , uint32_t *pCameraCnt
        , GENICAM_EProtocolType interfaceType);
```

功能说明：

    发现相机，执行成功返回值为 0，失败返回值小于 0；

    [in] thiz 系统单例指针；

    [out] ppCameraList 发现的相机列表；

    [out] pCameraCnt 相机个数；

    [in] interfaceType 需要发现的相机类型。

示例：

> GENICAM_Camera * pCameraList = NULL;
> 
> uint32_t cameraCnt = 0;
> 
> ret = pSystem->discovery( pSystem, &pCameraList, &cameraCnt, typeAll );

## 3.3 设备连接 / 断开

### 3.3.1 连接

接口：

```cpp
int32_t (*connect)(struct GENICAM_Camera *thiz
        ,GENICAM_ECameraAccessPermission accessPermission);
```

功能说明：

    连接相机，成功时返回 0，失败时返回值小于 0；

    [in] thiz 相机对象指针；

    [in] accessPermissionk 以何种权限连接相机。

         目前只支持 accessPermissionControl 权限。

示例：

> pCamera = &pCameraList[ 0 ];
> 
> ret = pGetCamera->connect( pGetCamera, accessPermissionControl );

### 3.3.2 断开

接口：

```cpp
 int32_t (* disConnect)(struct GENICAM_Camera * thiz)
```

功能说明：

    断开相机，成功时返回 0，失败时返回值小于 0；

    [in] thiz 相机对象指针。

示例：

> ret = pGetCamera->disConnect( pGetCamera );

## 3.4 设置属性

SDK 提供了 2 种方式读写属性：

- 通过预先给定的常用属性节点；

- 通过通用属性方法。

### 3.4.1 使用常用属性节点

（1）确定属性节点

    打开相机客户端，找到要对应的属性，查看该属性属于哪一个属性类别，如获取像素高度信息，找到 Height 属性，属于 ImageFormatControl ，则要创建 ImageFormatControl 节点。

<img src="../../image cache/2022-09-23-11-28-31-image.png" title="" alt="" data-align="center">

（2）示例：

```cpp
/// 创建 ImageFormatControl 属性节点结构
GENICAM_ImageFormatControlInfo imageFormatControlInfo = { 0 };
imageFormatControlInfo.pCamera = _pCurrCamera; // _pCurrCamera 已连接的相机

/// 创建 ImageFormatControl 属性节点
GENICAM_ImageFormatControl * pImageFormatCtrl = NULL;
if (0 != GENICAM_createImageFormatControl( &imageFormatControlInfo
    , &pImageFormatCtrl ))
{
    // 调用后需释放内存
    pImageFormatCtrl->release(pImageFormatCtrl);
    return -1;
}

/// 获取属性操作对象
// 如果头文件中没有该属性对应的获取属性操作对象的接口，则说明没有提供，
// 需要通过通用属性方法来设置。
GENICAM_IntNode intNode = pImageFormatCtrl->height( pImageFormatCtrl );
if( 0 != intNode.isVald(&intNode) )
{
    // 调用后需释放内存
    pImageFormatCtrl->release(pImageFormatCtrl);
    intNode.release(&intNode);
    return -1;
}

/// 获取属性值
int64_t nHeight;
if (0 != intNode.getValue(&intNode, &nHeight))
{
    // 注意：需要调用 release 释放内存
    intNode.release(&intNode);
    return -1;
}

/// 设置属性值
if (0 != intNode.setValue(&intNode, nHeight))
{
    // 注意：需要调用 release 释放内存
    intNode.release(&intNode);
    return -1;
}

/// 释放内存
pImageFormatCtrl->release(pImageFormatCtrl);
intNode.release(&intNode);
```

整体流程：

1. 创建属性节点结构体：ImageFormatControlInfo；

2. 创建属性节点：ImageFormatControl —— 释放本身内存；

3. 获取属性操作对象：GENICAM_IntNode —— 释放节点与操作对象内存；

4. 获取属性值 getValue —— 释放节点与操作对象内存；

5. 设置属性值 setValue —— 释放节点与操作对象内存；

6. 释放节点与操作对象内存。 

### 3.4.2 使用通用属性

打开相机客户端，找到指定属性并选中，在属性列表下方会展示对应的相关信息，选中 Height 属性，Type 为 Integer。

<img src="../../image cache/2022-09-23-14-04-23-image.png" title="" alt="" data-align="center">

#### （1）整形属性

    以 Width 属性为例：

```cpp
/// 创建整数节点类型信息
GENICAM_IntNodeInfo intNodeInfo = { 0 };
intNodeInfo.pCamera = pCamera;
memcpy(intNodeInfo.attrName, "Width", sizeof("Width")); //属性名字

/// 创建整数节点类型对象
GENICAM_IntNode *pIntNode = NULL;
if (0 != GENICAM_createIntNode(&intNodeInfo, &pIntNode))
{
    return -1;
}

/// 获取属性值 getValue
/// 设置属性值 setValue
/// 要释放 pIntNode 内部对象内存 release 
```

#### （2）浮点型属性

    以 ExposureTime 属性为例：

```cpp
/// 创建浮点数节点类型信息
GENICAM_DoubleNodeInfo doubleNodeInfo = {0};
doubleNodeInfo.pCamera = pCamera;
memcpy(doubleNodeInfo.attrName, "ExposureTime", sizeof("ExposureTime"));

/// 创建浮点数节点类型对象
GENICAM_DoubleNode *pDoubleNode = NULL; 
if (0 != GENICAM_createDoubleNode(&doubleNodeInfo, &pDoubleNode))
{
    return -1;
}

/// 获取属性值 getValue
/// 设置属性值 setValue
/// 要释放 pIntNode 内部对象内存 release 
```

#### （3）布尔型属性

    以ReverseX属性为例：

```cpp
/// 创建布尔节点类型信息
GENICAM_BoolNodeInfo boolNodeInfo = {0};
boolNodeInfo.pCamera = pCamera;
memcpy(boolNodeInfo.attrName, "ReverseX", sizeof("ReverseX"));

/// 创建布尔节点类型对象
GENICAM_BoolNode *pBoolNode = NULL;
if (0 != GENICAM_createBoolNode(&boolNodeInfo, &pBoolNode))
{
    return -1;
}

/// 获取属性值 getValue
/// 设置属性值 setValue
/// 释放 pIntNode 内部对象内存 release 
```

#### （4）枚举型属性

    以 TriggerSelector 属性为例：

```cpp
/// 创建枚举节点类型信息
GENICAM_EnumNodeInfo enumNodeInfo = {0};
enumNodeInfo.pCamera = pCamera;
memcpy(enumNodeInfo.attrName, "TriggerSelector"
        , sizeof("TriggerSelector"));

/// 创建枚举节点类型对象
GENICAM_EnumNode *pEnumNode = NULL;
if (0 != GENICAM_createEnumNode(&enumNodeInfo, &pEnumNode))
{
    return -1;
}

/// 获取枚举属性 symbol 值
char triggerSelectorValue[256] = { 0 };
uint32_t maxCnt;
if (0 != pEnumNode->getValueSymbol(pEnumNode 
    , triggerSelectorValue, &maxCnt))
{
    // 注意：需要释放 pEnumNode 内部对象内存
    pEnumNode->release(pEnumNode);
    return -1;
}

/// 设置枚举属性 symbol 值
if (0 != pEnumNode->setValueBySymbol(pEnumNode, "FrameStart"))
{
    // 注意：需要释放 pEnumNode 内部对象内存
    pEnumNode->release(pEnumNode);
    return -1;
}
// 注意：需要释放 pEnumNode 内部对象内存
```

#### （5）字符型属性

    以 DeviceUserID 属性为例：

```cpp
/// 创建字符串节点类型信息
GENICAM_StringNodeInfo stringNodeInfo = {0};
stringNodeInfo.pCamera = pCamera;
memcpy(stringNodeInfo.attrName, "DeviceUserID", sizeof("DeviceUserID"));

/// 创建字符串节点类型对象
GENICAM_StringNode *pStringNode = NULL;
if (0 != GENICAM_createStringNode(&stringNodeInfo, &pStringNode))
{
    return -1;
}

/// 获取属性值
char szDeviceUserID[256] = { 0 };
uint32_t maxCnt;
if (0 != pStringNode->getValue(pStringNode, &szDeviceUserID, &maxCnt))
{
    // 需要释放 pStringNode 内部对象内存
    pStringNode->release(pStringNode);
    return -1;
}

/// 设置属性值 setValue
/// 释放内存 release
```

#### （6）命令型属性

    以 TriggerSoftware 属性为例：

```cpp
/// 创建命令节点类型信息
GENICAM_CmdNodeInfo cmdNodeInfo = {0};
cmdNodeInfo.pCamera = pCamera;
memcpy(cmdNodeInfo.attrName, "TriggerSoftware" 
        , sizeof("TriggerSoftware"));

/// 创建命令节点类型对象
GENICAM_CmdNode *pCmdNode = NULL;
if (0 != GENICAM_createCmdNode(&cmdNodeInfo, &pCmdNode))
{
    return -1;
}

/// 执行命令类型属性
if (0 != pCmdNode->execute(pCmdNode))
{
    // 释放 pCmdNode 内部对象内存
    pCmdNode->release(pCmdNode);
    return -1;
}

/// 释放内部对象内存
```

#### 3.4.3 属性节点信息

（A57 相机）13 个属性节点信息

| Property               | Name |
|:---------------------- |:----:|
| DeviceControl          |      |
| ImageFormatControl     |      |
| AcquistionControl      |      |
| DigitalIOControl       |      |
| EventControl           |      |
| LUTControl             |      |
| AnalogContrtol         |      |
| TransportLayerControl  |      |
| UserSetControl         |      |
| ChunkDataControl       |      |
| CounterAndTimerControl |      |
| ISPControl             |      |

## 3.5 采集图像

### 3.5.1 创建 / 销毁流通道

#### （1）创建流对象

```cpp
/// 创建流对象信息，指定通道号
GENICAM_StreamSourceInfo streamSourceInfo;
streamSourceInfo.pCamera = pCamera;
streamSourceInfo.channelId = 0;

/// 创建流对象
GENICAM_StreamSource *pStreamSource = NULL;
if (0 != GENICAM_createStreamSource(&streamSourceInfo, &pStreamSource))
{
    return -1;
}
```

#### （2）销毁流对象

```cpp
// 销毁流对象
pStreamSource->release(pStreamSource);
```

### 3.5.2 设置图像接收缓存个数

接口：

```cpp
int32_t(*setBufferCount)(struct GENICAM_StreamSource *thiz
        , uint32_t nSize)
```

功能说明：

    连接相机，执行成功返回值为 0，失败返回值为 -1；

    缓存个数一般使用默认值(8)，只有在图像分辨率较大时才使用该接口设置一个

        较小的缓存个数，并且要在开始拉流之前调用。

    [in] thiz 流对象指针；

    [in] nSize 缓存数量。

### 3.5.3 开始 / 采集图像

#### （1）开始采集图像

接口：

```cpp
int32_t (*startGrabbing)(struct GENICAM_StreamSource *thiz
        , uint64_t maxImagesGrabbed
        , GENICAM_EGrabStrategy strategy);
```

功能说明：

    开始抓图，执行成功返回值为 0，失败返回值为 -1；

    [in] thiz 流对象指针；

    [in] maxImagesGrabbed 允许最多的抓图数，固定设置为 0；

    [in] strategy 抓图策略。

          grabStrartegySequential 表示按到达顺序处理图片；

          grabStrartegyLatestImage 表示获取获取最新的图片。

#### （2）停止采集图像

接口：

```cpp
int32_t (*stopGrabbing)(struct GENICAM_StreamSource *thiz);
```

功能说明：

    停止抓图。执行成功返回值为 0，失败返回值为 -1；

    [in] thiz 流对象指针。

### 3.5.4 获取图像

#### （1）主动采图

接口：

```cpp
int32_t (*getFrame)(struct GENICAM_StreamSource *thiz
        , GENICAM_Frame **ppFrame 
        , uint32_t timeoutMS);
```

功能说明：

    获取一帧图像。执行成功返回值为 0，失败返回值为 -1；

    [in] thiz 流对象指针；

    [out] ppFrame 一帧图像,内存由函数内部分配，用完该帧后需要调用 release 接

        口显示释放；

    [in] timeoutMS 获取一帧图像的超时时长,单位 MS。当值为 INFINITE 时表示无

        限等待。

#### （2）注册回调采图

① 注册数据帧回调函数

接口一：

```cpp
int32_t (*attachGrabbing)(struct GENICAM_StreamSource *thiz
        , callbackFun proc);
```

功能说明：

    注册数据帧回调函数。执行成功返回值为 0，失败返回值为 -1；

    注意：该接口需要在开始拉流之前调用

    [in] thiz 流对象指针； 

    [out] proc 数据帧回调函数。建议不要在该函数中处理耗时的操作，

        否则会阻塞后续数据帧的实时性。

接口二：

```cpp
int32_t (*attachGrabbing)(struct GENICAM_StreamSource *thiz
        , callbackFun proc
        , void* pUser)
```

功能说明：

    注册数据帧回调函数，可传入用户数据用以区分相机。

    执行成功返回值为 0，失败返回值为 -1；

    注意：该接口需要在开始拉流之前调用

    [in] thiz 流对象指针；

    [out] proc 数据帧回调函数。建议不要在该函数中处理耗时的操作，

        否则会阻塞后续数据帧的实时性；

    [in] pUser 用户数据。

② 反注册数据帧回调函数

接口一：

```cpp
int32_t(*detachGrabbing)(struct GENICAM_StreamSource *thiz
        , callbackFun proc);
```

功能说明：

   反注册数据帧回调函数。执行成功返回值为 0，失败返回值为 -1；

    注意：该接口需要在停止拉流之前调用

    [in] thiz 流对象指针；

    [out] proc 数据帧回调函数。

接口二：

```cpp
int32_t (*attachGrabbing)(struct GENICAM_StreamSource *thiz
        , callbackFun proc
        , void* pUser)
```

功能说明：

    反注册数据帧回调函数，可传入用户数据用以区分相机。

    执行成功返回值为 0，失败返回值为 -1；

    注意：该接口需要在停止拉流之前调用

    [in] thiz 流对象指针；

    [in] pUser 用户数据。

#### （3）获取图像裸数据

使用主动采图或回调取图方，可以获取到相机采集的一帧图像数据（GENICAM_Frame 对象）。GENICAM_Frame 对象提供了获取图像的裸数据、裸数据长度、图像宽高等信息的接口。此处仅描述获取图像裸数据接口。

接口：

```cpp
const void* (*getImage)(struct GENICAM_Frame *thiz);
```

功能说明：

    返回该帧图片裸数据的内存首地址。

    [in] thiz 图像结构指针；

    补充说明：可以通过 getImageSize 接口，获取图像裸数据（RAW Data）的长度。

        这样从首地址开始，偏移相应的长度，就可以获得整个图像裸数据。

#### （4）图像格式转换

        当相机出图的图像格式为 Mono8 或者 RGB8 时，通过 3.5.4 - (3) 获得到的裸数据

（RAW Data）可以直接使用。

        当相机出图的图像格式为其他格式时，通过 3.5.4 - (3) 获得到的裸数据（RAW Data）

则要进一步使用 ImageConvert.dll，转换为所需的对应格式才能使用（BGR24、RGB24、

Mono8）。

        头文件：ImageConvert.h，路径参考 1.4 SDK（c 接口）对应的资源

接口：

| 函数名                      | 功能                                    |
| ------------------------ | ------------------------------------- |
| IMGCNV_ConvertToBGR24    | 转换为 BGR24 的转换函数                       |
| IMGCNV_ConvertToRGB24    | 转换为 RGB24 的转换函数                       |
| IMGCNV_ConvertToMono8    | 转换为 Mono8 的转换函数                       |
| IMGCNV_ConvertToBGR24_Ex | 转换为 BGR24 的转换函数，并支持选择转换 Bayer 格式所用的算法 |
| IMGCNV_ConvertToRGB24_Ex | 转换为 RGB24 的转换函数，并支持选择转换 Bayer 格式所用的算法 |
| IMGCNV_ConvertToMono8_Ex | 转换为 Mono8 的转换函数，并支持选择转换 Bayer 格式所用的算法 |

    转换到其他目标图像格式时，调用相应的图像格式转码接口。

```cpp
/// 分配转换所需 buffer

/// 目标格式为 BGR24、RGB24 时，需要缓存大小为 宽*高*3

/// 目标格式为 Mono8 时，需要缓存大小 宽*高
int nBGRBufferSize = pFrame->getImageWidth(pFrame) 
                    * pFrame->getImageHeight(pFrame) * 3;
uint8_t *pBGRbuffer = (uint8_t *)malloc(nBGRBufferSize);


/// 设置转换配置
IMGCNV_SOpenParam openParam;
openParam.width = pFrame->getImageWidth(pFrame);
openParam.height = pFrame->getImageHeight(pFrame);
openParam.paddingX = pFrame->getImagePaddingX(pFrame);
openParam.paddingY = pFrame->getImagePaddingY(pFrame);
openParam.dataSize = pFrame->getImageSize(pFrame);
openParam.pixelForamt = pFrame->getImagePixelFormat(pFrame); 

/// 转换
IMGCNV_EErr eStatus = IMGCNV_ConvertToBGR24(pFrame->getImage(pFrame),
                         &openParam,
                         pBGRbuffer,
                         &nBGRBufferSize);
if (eStatus != IMGCNV_SUCCESS)
{
    printf("image convert failed!");
    free(pBGRbuffer);
}
```

## 3.6 事件通知

### 3.6.1 设备连接状态事件

（1）设备连接状态事件回调函数定义

```cpp
// 设备连接状态事件回调函数
void onDeviceLinkNotify( const GENICAM_SConnectArg * pConnectArg
        , void * pUser )
{
    if( pConnectArg->m_event == offLine )
    {
        // 相机掉线

        // 销毁流对象、事件订阅对象

        // 反注册流事件回调、相机事件回调、拉流回调，并停止拉流
    }  
    else if( pConnectArg-> == onLine )
    {
        // 相机上线

        // 断开相机，重新连接相机

        // 重新创建流对象、事件订阅对象

        // 重新注册流事件回调、相机事件回调、拉流回调

        // 重新开始拉流
    }
}
```

（2）注册设备连接状态事件回调

```cpp
// 事件订阅对象参数
GENICAM_EventSubscribeInfo eventSubscribeInfo = { 0 };
eventSubscribeInfo.pCamera = pCamera;

// 创建事件订阅对象GENICAM_EventSubscribe *pEventSubscribe = NULL;
if (0 != GENICAM_createEventSubscribe(&eventSubscribeInfo 
        , &pEventSubscribe))
{
 return -1;
}

// 注册设备连接状态事件回调
if ( 0 != pEventSubscribe->subscribeConnectArgsEx( pEventSubscribe
 , onDeviceLinkNotify, "Camera1" ) )
{
    pEventSubscribe->release(pEventSubscribe);
    return -1;
}
```

（3）反注册设备连接状态事件回调

```cpp
// 取消注册设备连接状态事件回调
if (0 != pEventSubscribe->unsubscribeConnectArgsEx( pEventSubscribe 
        , onDeviceLinkNotify, "Camera1" ) )
{
    pEventSubscribe->release(pEventSubscribe);
    return -1;
}

pEventSubscribe->release(pEventSubscribe);
```

### 3.6.2 流事件

流事件通知：

| Name                          | Role       |
| ----------------------------- | ---------- |
| streamEventLostPacket         | 传输层丢包引起的丢帧 |
| streamEventStreamChannelError | U3V 通信链路异常 |

（1）流通道事件回调函数定义

```cpp
// 流通道事件回调函数
void onStreamEvent(const GENICAM_SStreamArg* pStreamArg, void *pUser)
{
    if (pStreamArg->eStreamEventStatus == streamEventLostPacket)
    {
        // GigE 相机传输层丢包导致丢帧
    }
    else if (pStreamArg->eStreamEventStatus 
            == streamEventStreamChannelError)
    {
        // USB 相机链路错误
        // 此处一般要停止拉流，并重新开始拉流。
    } 
}
```

（2）注册流通道事件回调

```cpp
// 事件订阅对象参数
GENICAM_EventSubscribeInfo eventSubscribeInfo = { 0 };
eventSubscribeInfo.pCamera = pCamera; 

// 创建事件订阅对象
GENICAM_EventSubscribe * pEventSubscribe = NULL;
if ( 0 != GENICAM_createEventSubscribe(
        &eventSubscribeInfo, &pEventSubscribe) )
{
    return -1;
}

// 注册流通道事件回调
if ( 0 != pEventSubscribe->subscribeStreamArgEx(
        pEventSubscribe, onStreamEvent, "Camera1") )
{
    // 释放内存
}
```

（3）反注册流通道事件回调并销毁事件订阅对象

```cpp
// 取消注册流通道事件回调
if (0 != pEventSubscribe->unsubscribeStreamArgEx( 
        pEventSubscribe, onStreamEvent, "Camera1"))
{
    // 释放内存
}
// 释放内存 
```

### 3.6.3 相机消息事件

相机提供了一些内部消息事件，例如曝光结束、帧开始等事件通知。使用相机消息事件之前，需要将对应的相机事件开关设置为 On。例如要获取相机曝光结束的事件通知，则设置 EventSelector为 ExposureEnd，并设置 EventNotification 为 On，如下图：

<img src="../../image cache/2022-09-30-16-24-58-image.png" title="" alt="" data-align="center">

（1）消息通道事件回调定义

```cpp
// 消息通道事件回调
void onMsgChannel(const GENICAM_SMsgChannelArg* pMsgChannelArg 
        , void *pUser)
{
    if (pMsgChannelArg->eventID == MSG_EVENT_ID_EXPOSURE_END)
    {
        // 曝光结束事件通知
    } 
}
```

（2）注册消息通道事件回调

```cpp
// 事件订阅对象参数
GENICAM_EventSubscribeInfo eventSubscribeInfo = { 0 };
eventSubscribeInfo.pCamera = pCamera; 

// 创建事件订阅对象
GENICAM_EventSubscribe *pEventSubscribe = NULL;
if (0 != GENICAM_createEventSubscribe(&eventSubscribeInfo
        , &pEventSubscribe))
{
    return -1;
}

// 注册消息通道事件回调
if (0 != pEventSubscribe->subscribeMsgChannelEx(pEventSubscribe
        , onMsgChannel, "Camera1"))
{
    // 释放内存
}
```

（3）反注册消息通道事件回调并销毁事件订阅对象

```cpp
// 取消注册消息通道事件回调
if (0 != pEventSubscribe->unsubscribeMsgChannelEx(pEventSubscribe 
        , onMsgChannel, "Camera1"))
{
 // 释放内存
}
// 释放内存
```
