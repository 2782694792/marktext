# GENICAM_GigEInterface

> GigE 相机接口类    （32位系统下128字节）

| 名称        | 参数                                    | 作用    |
| --------- | ------------------------------------- | ----- |
| [in] thiz | (struct GENICAM_GigEInterface * thiz) | 本接口指针 |

# 一、接口操作

## 1. priv

> /// \brief      底层私有数据

```cpp
void * priv;
```

## 2. addRef

> /// \brief      增加接口引用

```cpp
int32_t( *addRef )( struct GENICAM_GigEInterface * thiz );
```

## 3. release

> /// \brief      释放接口

```cpp
int32_t( *release )( struct GENICAM_GigEInterface * thiz );
```

# 二、网卡

## 4. getDescription

> /// \brief      获取网卡描述信息

> /// \return    返回网卡描述信息，失败返回NULL

```cpp
const char * ( *getDescription )( struct GENICAM_GigEInterface * thiz );
```

## 5. getIpAddress

> /// \brief      获取网卡的IP地址

> /// \return    返回网卡的IP地址，失败返回NULL

```cpp
const char * ( *getIpAddress )( struct GENICAM_GigEInterface * thiz );
```

## 6. getSubnetMask

> /// \brief      获取网卡的子网掩码

> /// \return    返回网卡的子网掩码，失败返回NULL

```cpp
const char * ( *getSubnetMask )( struct GENICAM_GigEInterface * thiz );
```

## 7. getGateway

> /// \brief      获取网卡的网关

> /// \return    返回网卡的网关，失败返回NULL

```cpp
const char * ( *getGateway )( struct GENICAM_GigEInterface * thiz );
```

## 8. getMacAddress

> /// \brief      获取网卡的Mac地址

> /// \return    返回网卡的Mac地址，失败返回NULL

```cpp
const char * ( *getMacAddress )( struct GENICAM_GigEInterface * thiz );
```

# 三、保存

## 9. reserve[ 24 ]

> /// \brief      预留字段

```cpp
uint32_t reserve[ 24 ];
```
