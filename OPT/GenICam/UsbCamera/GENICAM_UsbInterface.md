# GENICAM_UsbCamera

> Usb 相机接口类    （32位系统下128字节）

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

## 4. getDescription

> /// \brief      获取描述信息

> /// \return    返回描述信息，失败返回 NULL

```cpp
const char * ( *getDescription)( struct GENICAM_UsbCamera * thiz );
```

## 5. getVendorID

> /// \brief      获取 Vendor ID

> /// \return    返回 Vendor ID，失败返回 NULL

```cpp
const char * ( *getVendorID)( struct GENICAM_UsbCamera * thiz );
```

## 6. getDeviceID

> /// \brief      获取 Device ID

> /// \return    返回 Device ID，失败返回 NULL

```cpp
const char * ( *getDeviceID)( struct GENICAM_UsbCamera * thiz );
```

## 7. getSubsystemID

> /// \brief      获取 Subsystem ID

> /// \return    返回 Subsystem ID，失败返回 NULL

```cpp
const char * ( *getSubsystemID)( struct GENICAM_UsbCamera * thiz );
```

## 8. getRevision

> /// \brief      获取 Revision

> /// \return    返回 Revision，失败返回 NULL

```cpp
const char * ( *getRevision)( struct GENICAM_UsbCamera * thiz );
```

## 9. getDescription

> /// \brief      获取接口的 speed

> /// \return    返回接口的 speed，失败返回 NULL

```cpp
const char * ( *getSpeed)( struct GENICAM_UsbCamera * thiz );
```

# 三、保存

## 10. reserve[ 23 ]

> /// \brief      预留字段

```cpp
uint32_t reserve[ 23 ];		
```
