# GENICAM_StreamSource

> 流对象（32位系统下128字节）

| 名称        | 参数                                   | 作用    |
| --------- | ------------------------------------ | ----- |
| [in] thiz | (struct GENICAM_StreamSource * thiz) | 本接口指针 |

# 一、接口操作

## 1. priv

> /// \brief      底层私有数据

```cpp
void * priv;
```

## 2. addRef

> /// \brief      增加接口引用

```cpp
int32_t( *addRef )( struct GENICAM_StreamSource * thiz );
```

## 3. release

> /// \brief      释放接口

```cpp
int32_t( *release )( struct GENICAM_StreamSource * thiz );
```

# 二、图像操作

## 4. startGrabbing

> /// \brief      开始抓图

> /// \param    [in] maxImagesGrabbed 允许最多的抓图数，达到指定抓图数
> 
>                     后停止抓图，如果为 0，表示忽略此参数，会连续抓图
> 
> /// \param    [in] strategy 抓图策略

> /// \return    返回抓图是否成功，0 表示成功，-1 表示失败

```cpp
int32_t( *startGrabbing )( struct GENICAM_StreamSource * thiz
        , uint64_t maxImagesGrabbed
        , GENICAM_EGrabStrategy strategy );
```

## 5. stopGrabbing

> /// \brief      停止抓图

> /// \return    返回停止抓图是否成功，0 表示成功，-1 表示失败

```cpp
int32_t( *stopGrabbing )( struct GENICAM_StreamSource * thiz );
```

## 6. isGrabbing

> /// \brief      是否正在抓图

> /// \return    返回是否处于拉流状态，0 表示处于拉流状态，-1 表示非拉流状态

```cpp
int32_t( *isGrabbing )( struct GENICAM_StreamSource * thiz );
```

## 7. getFrame

> /// \brief      获取一帧图像，该接口不支持多线程调用

> /// \param    [out] ppFrame 一帧图像,内存由函数内部分配，用完该帧
> 
>                     后需要显示调用GENICAM_Frame release接口显示释放
> /// \param    [in]  timeoutMS 获取一帧图像的超时时长,单位 MS ,
> 
>                     当值为 INFINITE 时表示无限等待

> /// \return    返回是否成功，0 表示成功，-1 表示失败

```cpp
int32_t( *getFrame )( struct GENICAM_StreamSource * thiz
        , GENICAM_Frame ** ppFrame
        , uint32_t timeoutMS );
```

## 8. attachGrabbing

> /// \brief      注册数据帧回调函数。该异步获取帧机制和同步获取帧机制互斥，
> 
>                     系统中两者只能选其一。
> /// \brief      只能处理一个回调函数

> /// \param    [in] proc 数据帧回调函数，建议不要在该函数中处理耗时的操作，
> 
>                     否则会阻塞后续数据帧的实时性

> /// \return    返回注册是否成功，0 表示成功，-1 表示失败

```cpp
int32_t( *attachGrabbing )( struct GENICAM_StreamSource * thiz
        , callbackFun proc );
```

## 9. detachGrabbing

> /// \brief      去注册数据帧回调函数

> /// \param    [in] proc 去注册数据帧回调函数

> /// \return    返回去注册是否成功，0 表示成功，-1 表示失败

```cpp
int32_t( *detachGrabbing )( struct GENICAM_StreamSource * thiz
        , callbackFun proc );
```

## 10. attachGrabbingEx

> /// \brief      注册数据帧回调函数(包含用户自定义数据)。该异步获取帧机制
> 
>                     和同步获取帧机制互斥，系统中两者只能选其一。
> /// \brief      只能处理一个回调函数

> /// \param    [in] proc 数据帧回调函数，建议不要在该函数中处理耗时的操作，
> 
>                     否则会阻塞后续数据帧的实时性
> /// \param    [in] pUser 用户自定义数据

> /// \return    返回注册是否成功，0 表示成功，-1 表示失败

```cpp
int32_t( *attachGrabbingEx )( struct GENICAM_StreamSource * thiz
        , callbackFunEx proc
        , void * pUser );
```

## 11. detachGrabbingEx

> /// \brief      去注册数据帧回调函数(包含用户自定义数据)

> /// \param    [in] proc 去注册数据帧回调函数
> /// \param    [in] pUser 用户自定义数据(与 attachGrabbingEx 的 pUser 相同)

> /// \return    返回去注册是否成功，0 表示成功，-1 表示失败

```cpp
int32_t( *detachGrabbingEx )( struct GENICAM_StreamSource * thiz
        , callbackFunEx proc
        , void * pUser );
```

# 三、设置

## 12. setBufferCount

> /// \brief      设置缓存个数,不能在拉流过程中设置

> /// \param    [in]  缓存数量

> /// \return    返回是否成功

```cpp
int32_t( *setBufferCount )( struct GENICAM_StreamSource * thiz
        , uint32_t nSize );
```

## 13. setInterPacketTimeout

> /// \brief      设置驱动包间隔时间(ms),仅对 Gige 设备有效

> /// \param    [in] nTimeout 包间隔时间，单位 ms

> /// \return    新设置是否成功,0 表示成功，-1 表示失败

```cpp
int32_t( *setInterPacketTimeout )( struct GENICAM_StreamSource * thiz
        , uint32_t nTimeout );
```

# 四、保存

## 14. reserved[ 19 ]

> /// \brief      记录保留

```cpp
uint32_t reserved[ 19 ];
```
