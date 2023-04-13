# GENICAM_EventSubscribe

> 事件订阅对象（32位系统下128字节）

| 名称        | 参数                                     | 作用    |
| --------- | -------------------------------------- | ----- |
| [in] thiz | (struct GENICAM_EventSubscribe * thiz) | 本接口指针 |

# 一、接口操作

## 1. priv

> /// \brief      底层私有数据

```cpp
void * priv;
```

## 2. addRef

> /// \brief      增加接口引用

```cpp
int32_t( *addRef )( struct GENICAM_EventSubscribe * thiz );
```

## 3. release

> /// \brief      释放接口

```cpp
int32_t( *release )( struct GENICAM_EventSubscribe  * thiz );
```

# 二、设备连接状态事件

## 4. subscribeConnectArgs

> /// \brief      设备连接状态事件回调注册,只支持一个回调函数有效

> /// \param    [in] proc  设备连接状态事件回调注册函数

> /// \return    注册是否成功

```cpp
int32_t( *subscribeConnectArgs )( struct GENICAM_EventSubscribe * thiz
        , const connectCallBack proc );
```

## 5. unsubscribeConnectArgs

> /// \brief      设备连接状态事件回调去注册

> /// \param    [in] proc  设备连接状态事件回调去注册函数

> /// \return    去注册是否成功

```cpp
int32_t( *unsubscribeConnectArgs )( struct GENICAM_EventSubscribe * thiz
        , const connectCallBack proc );
```

## 6. subscribeConnectArgs

> /// \brief      设备连接状态事件回调注册,只支持一个回调函数有效

> /// \param    [in] proc  设备连接状态事件回调注册函数
> 
> /// \param    [in] pUser   用户自定义数据

> /// \return    注册是否成功

```cpp
int32_t( *subscribeConnectArgs )( struct GENICAM_EventSubscribe * thiz
        , const connectCallBack proc
        , void * pUser );
```

## 7. unsubscribeConnectArgs

> /// \brief      设备连接状态事件回调去注册

> /// \param    [in] proc  设备连接状态事件回调去注册函数
> 
> /// \param    [in] pUser   用户自定义数据

> /// \return    去注册是否成功

```cpp
int32_t( *unsubscribeConnectArgs )( struct GENICAM_EventSubscribe * thiz
        , const connectCallBack proc
        , void * pUser );
```

# 三、参数更新事件

## 8. subscribeParamUpdate

> /// \brief      参数更新事件回调注册,只支持一个回调函数有效

> /// \param    [in] proc 参数更新注册的事件回调函数

> /// \return    注册是否成功

```cpp
int32_t( *subscribeParamUpdate )( struct GENICAM_EventSubscribe * thiz
    , const paramUpdateCallBack proc );
```

## 9. unsubscribeParamUpdate

> /// \brief      参数更新事件回调去注册

> /// \param    [in] proc 参数更新事件去注册的回调函数

> /// \return    去注册是否成功

```cpp
int32_t( *unsubscribeParamUpdate )( struct GENICAM_EventSubscribe * thiz
        , const paramUpdateCallBack proc );
```

## 10. subscribeParamUpdate

> /// \brief      参数更新事件回调注册,只支持一个回调函数有效

> /// \param    [in] proc 参数更新注册的事件回调函数
> 
> /// \param    [in] pUser   用户自定义数据

> /// \return    注册是否成功

```cpp
int32_t( *subscribeParamUpdate )( struct GENICAM_EventSubscribe * thiz
        , const paramUpdateCallBack proc
        , void * pUser );
```

## 11. unsubscribeParamUpdate

> /// \brief      参数更新事件回调去注册

> /// \param    [in] proc 参数更新事件去注册的回调函数
> 
> /// \param    [in] pUser   用户自定义数据

> /// \return    去注册是否成功

```cpp
int32_t( *unsubscribeParamUpdate )( struct GENICAM_EventSubscribe * thiz
        , const paramUpdateCallBack proc
        , void * pUser );
```

# 四、流通道事件

## 12. subscribeStreamArg

> /// \brief      流通道事件回调注册,只支持一个回调函数有效

> /// \param    [in] proc 流通道事件回调注册函数

> /// \return    注册是否成功

```cpp
int32_t( *subscribeStreamArg )( struct GENICAM_EventSubscribe * thiz
        , const streamCallBack proc );
```

## 13. unsubscribeStreamArg

> /// \brief      流通道事件回调去注册

> /// \param    [in] proc 流通道事件回调去注册函数

> /// \return    去注册是否成功

```cpp
int32_t( *unsubscribeStreamArg )( struct GENICAM_EventSubscribe * thiz
        , const streamCallBack proc );
```

## 14. subscribeStreamArg

> /// \brief      流通道事件回调注册,只支持一个回调函数有效

> /// \param    [in] proc 流通道事件回调注册函数
> 
> /// \param    [in] pUser   用户自定义数据

> /// \return    注册是否成功

```cpp
int32_t( *subscribeStreamArg )( struct GENICAM_EventSubscribe * thiz
        , const streamCallBack proc
        , void * pUser );
```

## 15. unsubscribeStreamArg

> /// \brief      流通道事件回调去注册

> /// \param    [in] proc 流通道事件回调去注册函数
> 
> /// \param    [in] pUser   用户自定义数据

> /// \return    去注册是否成功

```cpp
int32_t( *unsubscribeStreamArg )( struct GENICAM_EventSubscribe * thiz
        , const streamCallBack proc
        , void * pUser );
```

# 五、消息通道事件

## 16. subscribeMsgChannelEx

> /// \brief      消息通道事件回调注册

> /// \param    [in] proc  消息通道事件回调注册函数

> /// \return    注册是否成功

```cpp
int32_t( *subscribeMsgChannelEx )( struct GENICAM_EventSubscribe * thiz
        , const msgChannelCallBackEx proc
        , void * pUser );
```

## 17. unsubscribeMsgChannelEx

> /// \brief      消息通道事件回调去注册

> /// \param    [in] proc  消息通道事件回调去注册函数

> /// \return    去注册是否成功

```cpp
int32_t( *unsubscribeMsgChannelEx )( struct GENICAM_EventSubscribe * thiz
        , const msgChannelCallBackEx proc
        , void * pUser );
```

# 六、保存

## 18. reserve[ 15 ]

> /// \brief      预留字段

```cpp
uint32_t reserve[ 15 ];
```
