# GENICAM_UsbCamera

> Usb 相机对象操作接口    （32位系统下128字节）

| 名称        | 参数                                | 作用    |
| --------- | --------------------------------- | ----- |
| [in] thiz | (struct GENICAM_UsbCamera * thiz) | 本接口指针 |

# 一、接口操作

## 1. priv

> /// \brief      底层私有数据

```cpp
void * priv;
```

## 2. addRef

> /// \brief      增加接口引用

```cpp
int32_t( *addRef )( struct GENICAM_UsbCamera * thiz );
```

## 3. release

> /// \brief      释放接口

```cpp
int32_t( *release )( struct GENICAM_UsbCamera * thiz );
```

# 二、相机信息

## 4. getConfigurationValid

> /// \brief      获取相机的 Configuration Vali

> /// \return    返回相机的 Configuration Valid，失败返回 NULL

```cpp
const char * ( *getConfigurationValid )( struct GENICAM_UsbCamera * thiz );
```

## 5. getGenCPVersion

> /// \brief      获取相机的 GenCP Version

> /// \return    返回相机的 GenCP Version，失败返回 NULL

```cpp
const char * ( *getGenCPVersion )( struct GENICAM_UsbCamera * thiz );
```

## 6. getU3VVersion

> /// \brief      获取相机的 U3V Version

> /// \return    返回相机的 U3V Version，失败返回 NULL

```cpp
const char * ( *getU3VVersion )( struct GENICAM_UsbCamera * thiz );
```

## 7. getDeviceGUID

> /// \brief      获取相机的 Device GUID

> /// \return    返回相机的 Device GUID，失败返回 NULL

```cpp
const char * ( *getDeviceGUID )( struct GENICAM_UsbCamera * thiz );
```

## 8. getFamilyName

> /// \brief      获取相机的 Family Name

> /// \return    返回相机的 Family Name，失败返回 NULL

```cpp
const char * ( *getFamilyName )( struct GENICAM_UsbCamera * thiz );
```

## 9. getU3VSerialNumber

> /// \brief      获取相机的序列号

> /// \return    返回相机的序列号，失败返回 NULL

```cpp
const char * ( *getU3VSerialNumber )( struct GENICAM_UsbCamera * thiz );
```

## 10. getSpeed

> /// \brief      获取相机的 Speed

> /// \return    返回相机的 Speed，失败返回 NULL

```cpp
const char * ( *getSpeed )( struct GENICAM_UsbCamera * thiz );
```

## 11. getMaxPower

> /// \brief      获取相机的 Max Power

> /// \return    返回相机的 Max Power，失败返回 NULL

```cpp
const char * ( *getMaxPower )( struct GENICAM_UsbCamera * thiz );
```

# 三、相机支持

## 12. isLowSpeedSupported

> /// \brief      相机是否支持 Low Speed

> /// \return    0 支持， -1 不支持

```cpp
int32_t( *isLowSpeedSupported )( struct GENICAM_UsbCamera * thiz );
```

## 13. isFullSpeedSupported

> /// \brief      相机是否支持 Full Speed

> /// \return    0 支持， -1 不支持

```cpp
int32_t( *isFullSpeedSupported)( struct GENICAM_UsbCamera * thiz );
```

## 14. isHighSpeedSupported

> /// \brief      相机是否支持 High Speed

> /// \return    0 支持， -1 不支持

```cpp
int32_t( *isHighSpeedSupported)( struct GENICAM_UsbCamera * thiz );
```

## 15. isSuperSpeedSupported

> /// \brief      相机是否支持 Super Speed

> /// \return    0 支持， -1 不支持

```cpp
int32_t( *isSuperSpeedSupported)( struct GENICAM_UsbCamera * thiz );
```

## 16. isDriverInstalled

> /// \brief      是否安装了 USB 驱动

> /// \return    0 安装了 USB 驱动，-1 没有安装 USB 驱动

```cpp
nt32_t( *isDriverInstalled )( struct GENICAM_UsbCamera * thiz );
```

# 四、保存

## 17. reserve[ 16 ];

> /// \brief      预留字段

```cpp
uint32_t reserve[ 16 ];
```
