# GENICAM_System

> 全局单例系统对象（32位系统下128字节）

| 名称        | 参数                             | 作用    |
| --------- | ------------------------------ | ----- |
| [in] thiz | (struct GENICAM_System * thiz) | 本接口指针 |

# 一 、接口操作

## 1. priv

> /// \brief      底层私有数据

```cpp
void * priv;
```

## 2. addRef

> /// \brief      增加接口引用

```cpp
int32_t( *addRef )( struct GENICAM_System * thiz );
```

## 3. release

> /// \brief      释放接口

```cpp
int32_t( *release )( struct GENICAM_System * thiz );
```

# 二、设备操作

## 4. discovery

> /// \brief      发现指定接口类型可达的设备
> 
>                     1、该接口不能与enumDevicesInfo、createDevice接口同时使用；
> 
>                     2、设备对象由discovery接口内部创建。

> /// \param    [out] ppCameraList 指定接口类型所有在线设备对象列表,该内存
> 
>                     在函数内部申请，但需外部传入一个一维指针有效地址指向该设备列表；
> 
> /// \param    [out] pCameraCnt  发现的设备个数；
> 
> /// \param    [in]  interfaceType  接口类型，当传入的是 typeAll 时，表示
> 
>                     目前所有已支持的协议类型设备都会被发现

> /// \retval      < 0   发现设备失败
> /// \retval      = 0   发现设备成功

```cpp
int32_t( *discovery )( struct GENICAM_System * thiz
        , GENICAM_Camera ** ppCameraList
        , uint32_t * pCameraCnt
        , GENICAM_EProtocolType interfaceType );
```

## 5. getCamera

> /// \brief      根据相机的 key 获取 Camera 对象指针

> /// \param    [in] pCameraKey 相机的key

> /// \return    返回 Camera 指针,若传入的 pCameraKey 不存在或者非法，
> 
>                     则返回空指针

```cpp
GENICAM_Camera * ( *getCamera )( struct GENICAM_System * thiz
            , const char * pCameraKey );
```

## 6. getVersion

> /// \brief      获取版本信息

> /// \return    版本信息，失败时返回NULL

```cpp
const char * ( *getVersion )( struct GENICAM_System * thiz );
```

## 7. enumDevicesInfo

> /// \brief       发现指定接口类型可达设备的设备信息        
> 
>                     1、该接口不能与 discovery 接口同时使用；
> 
>                     2、该接口不会创建设备对象，只返回 ppDevInfoList 设备信息，用户需
> 
>                          通过设备信息调用 createDevice 接口来创建设备对象。

> /// \param    [out] ppDevInfoList 指定接口类型所有在线设备的设备信息列表
> 
> /// \param    [out] pCameraCnt  发现的设备个数
> /// \param    [in]  interfaceType  接口类型，当传入的是 typeAll 时，
> 
>                     表示目前所有已支持的协议类型设备都会被发现

> /// \retval      < 0   发现设备信息失败
> /// \retval      = 0   发现设备信息成功

```cpp
int32_t( *enumDevicesInfo )( struct GENICAM_System * thiz
        , DeviceInfo ** ppDevInfoList
        , uint32_t * pCameraCnt
        , GENICAM_EProtocolType interfaceType );
```

## 8. createDevice

> /// \brief      创建设备对象。
> 
> /// \brief      1、该接口不能与 discovery 接口同时使用。
> 
> /// \brief      2、由该接口创建的设备对象需要调用相应的 release 接口来释放，
> 
>                         以防内存泄漏。

> /// \param     [in] pDevInfo 设备信息
> 
> /// \param     [out] ppCamera 创建的设备对象

> /// \retval       < 0   创建失败
> /// \retval       = 0   创建成功

```cpp
int32_t( *createDevice )( struct GENICAM_System * thiz
        , DeviceInfo * pDevInfo
        , GENICAM_Camera ** ppCamera );
```

# 三、保存

## 9. reserved[ 24 ]

> /// \brief      记录保留

```cpp
uint32_t reserved[ 24 ];
```
