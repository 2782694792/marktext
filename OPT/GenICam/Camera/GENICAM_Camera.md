# GENICAM_Camera

> 通用相机对象（32位系统下128字节）

| 名称        | 参数                             | 作用    |
| --------- | ------------------------------ | ----- |
| [in] thiz | (struct GENICAM_Camera * thiz) | 本接口指针 |

# 一、接口操作

## 1. priv

> /// \brief      底层私有数据

```cpp
void * priv;
```

## 2. addRef

> /// \brief      增加接口引用

```cpp
int32_t( *addRef )( struct GENICAM_Camera * thiz );
```

## 3. release

> /// \brief      释放接口

```cpp
int32_t( *release )( struct GENICAM_Camera * thiz );
```

# 二、相机信息

## 4. getType

> /// \brief      得到相机类型

> /// \return    成功则返回 0，否则返回 -1

```cpp
int32_t( *getType )( struct GENICAM_Camera * thiz );
```

## 5. getName

> /// \brief      获取相机名称

> /// \return    成功返回相机名称，否则返回 NULL

```cpp
const char * ( *getName )( struct GENICAM_Camera * thiz );
```

## 6. getKey

> /// \brief      获取相机的唯一标识

> /// \return    成功返回唯一标识相机的字符串，失败返回 NULL

```cpp
const char * ( *getKey )( struct GENICAM_Camera * thiz );
```

## 7. getInterfaceName

> /// \brief      获取相机所属的接口名

> /// \return    成功返回设备所属的接口名，否则返回NULL

```cpp
const char * ( *getInterfaceName )( struct GENICAM_Camera * thiz );
```

## 8. getVendorName

> /// \brief      获取相机制造厂商名称

> /// \return    返回厂商名称

```cpp
const char * ( *getVendorName )( struct GENICAM_Camera * thiz );
```

## 9. getModelName

> /// \brief      获取相机的型号

> /// \return    返回相机的型号

```cpp
const char * ( *getModelName )( struct GENICAM_Camera * thiz );
```

## 10. getSerialNumber

> /// \brief      获取相机的序列号

> /// \return    返回相机的序列号

```cpp
const char * ( *getSerialNumber )( struct GENICAM_Camera * thiz );
```

## 11. getDeviceVersion

> /// \brief      获取相机固件版本号

> /// \return    返回相机固件版本号

```cpp
const char * ( *getDeviceVersion )( struct GENICAM_Camera * thiz );
```

## 12. getManufactureInfo

> /// \brief      获取相机制造信息

> /// \return    返回相机制造信息

```cpp
const char * ( *getManufactureInfo )( struct GENICAM_Camera * thiz );
```

## 13. getInterfaceType

> /// \brief      获取相机所连接的接口类型

> /// \return    返回相机所连接的接口类型

```cpp
int32_t( *getInterfaceType )( struct GENICAM_Camera * thiz );
```

# 三、相机操作

## 14. connect

> /// \brief      连接设备，与相机建立连接关系，
> 
>                     如 Gige Vision 协议的相机即是获得控制权限、创建控制通道

> /// \param    [in] accessPermission 目前只支持 accessPermissionControl 权限，
> 
>                     调用该接口时传入此类权限

> /// \retval      < 0 连接设备失败
> /// \retval      = 0 连接设备成功

```cpp
int32_t( *connect )( struct GENICAM_Camera * thiz
        , GENICAM_ECameraAccessPermission accessPermission );
```

## 15. disConnect

> /// \brief      断开连接，断开与相机的连接关系，
> 
>                     如 Gige Vision 协议的相机即是释放控制权限、销毁控制通道

> /// \retval      < 0 断开设备失败
> /// \retval      = 0 断开设备成功

```cpp
int32_t( *disConnect )( struct GENICAM_Camera * thiz );
```

## 16. isConnected

> /// \brief      判断相机当前是否处于已连接状态

> /// \retval      < 0 设备处于断开状态
> /// \retval      = 0 设备处于连接状态

```cpp
int32_t( *isConnected )( struct GENICAM_Camera * thiz );
```

# 四、保存

## 17. downLoadGenICamXML

> /// \brief      下载相机描述 XML 文件，并保存到指定路径

> /// \param    [in] aFullFilename 文件要保存的路径

> /// \retval      < 0 下载 XML 失败
> /// \retval      = 0 下载 XML 成功

```cpp
int32_t( *downLoadGenICamXML )( struct GENICAM_Camera * thiz
        , const char * aFullFilename );
```

## 18. reserved[ 15 ]

> /// \brief      记录保留

```cpp
uint32_t reserved[ 15 ];
```
