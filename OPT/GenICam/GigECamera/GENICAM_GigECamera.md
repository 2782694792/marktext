# GENICAM_GigECamera

> GigE 相机对象操作接口（32位系统下128字节）

| 名称        | 参数                                 | 作用    |
| --------- | ---------------------------------- | ----- |
| [in] thiz | (struct GENICAM_GigECamera * thiz) | 本接口指针 |

# 一、接口操作

## 1. priv

> /// \brief      底层私有数据

```cpp
void * priv;
```

## 2. addRef

> /// \brief      增加接口引用

```cpp
int32_t( *addRef )( struct GENICAM_GigECamera * thiz );
```

## 3. release

> /// \brief      释放接口

```cpp
int32_t( *release )( struct GENICAM_GigECamera * thiz );
```

# 二、相机网络

## 4. getIpAddress

> /// \brief      获取相机的ipAddress

> /// \return    返回 ipAddress 相机的IP地址，失败返回 NULL

```cpp
const char * ( *getIpAddress )( struct GENICAM_GigECamera * thiz );
```

## 5. getSubnetMask

> /// \brief      返回相机的子网掩码

> /// \return    返回 subnetMask 相机的子网掩码，失败返回 NULL

```cpp
const char * ( *getSubnetMask )( struct GENICAM_GigECamera * thiz );
```

## 6. getGateway

> /// \brief      返回相机的网关

> /// \return    返回 gateway 相机的网关，失败返回 NULL

```cpp
const char * ( *getGateway )( struct GENICAM_GigECamera * thiz );
```

## 7. getMacAddress

> /// \brief      获取相机的 macAddress

> /// \return    返回 macAddress 相机的Mac地址，失败返回 NULL

```cpp
const char * ( *getMacAddress )( struct GENICAM_GigECamera * thiz );
```

## 8. forceIpAddress

> /// \brief      修改设备IP
> 
>                     1、调用该函数时如果 newSubnetMask 和 newGateway 都设
> 
>                          置了有效值，则以此有效值为准；
> 
>                     2、调用该函数时如果 newSubnetMask 和 newGateway 都设
> 
>                          置了 NULL，则内部实现时用它所连接网卡的子网掩码和网关代替；
> 
>                     3、调用该函数时如果 newSubnetMask 和 newGateway 两者中
> 
>                          其中一个为 NULL，另一个非 NULL，则返回失败

> /// \param    [in] newIpAddress 新的IP地址信息
> /// \param    [in] newSubnetMask 新的掩码信息
> /// \param    [in] newGateway 新的网关信息

> /// \retval     < 0   修改IP失败
> /// \retval     = 0   修改IP成功

```cpp
int32_t( *forceIpAddress )( struct GENICAM_GigECamera * thiz
        , const char * newIpAddress
        , const char * newSubnetMask
        , const char * newGateway );
```

## 9. getIPConfiguration

> /// \brief      获取相机的 IP Configuration

> /// \return    返回相机的 IP Configuration，失败返回 NULL

```cpp
const char * ( *getIPConfiguration )( struct GENICAM_GigECamera * thiz );
```

# 三、相机信息

## 10. getProtocolVersion

> /// \brief      获取相机的 Protocol Version

> /// \return    返回相机的 Protocol Version，失败返回 NULL

```cpp
const char * ( *getProtocolVersion )( struct GENICAM_GigECamera * thiz );
```

## 11. getAccessPermission

> /// \brief      获取相机的当前访问权限

> /// \return    如果获取成功，返回的枚举值代表相机的当前访问权限，
> 
>                     如果失败，返回的枚举值为 accessPermissionUnknown

```cpp
GENICAM_ECameraAccessPermission( *getAccessPermission )
        ( struct GENICAM_GigECamera * thiz );
```

# 四、保存

## 12. reserve[ 21 ]

> /// \brief      预留字段

```cpp
uint32_t reserve[ 21 ];    
```
