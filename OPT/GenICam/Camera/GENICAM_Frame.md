# GENICAM_Frame

> 数据帧对象（32位系统下128字节）

| 名称        | 参数                            | 作用    |
| --------- | ----------------------------- | ----- |
| [in] thiz | (struct GENICAM_Frame * thiz) | 本接口指针 |

# 一 、接口操作

## 1. priv

> /// \brief      底层私有数据

```cpp
void * priv;
```

## 2. addRef

> /// \brief      增加接口引用

```cpp
int32_t( *addRef )( struct GENICAM_Frame * thiz );
```

## 3. release

> /// \brief      释放接口

```cpp
int32_t( *release )( struct GENICAM_Frame * thiz );
```

## 4.  clone

> /// \brief      帧深拷贝克隆接口

> /// \return    新的一帧对象

```cpp
struct GENICAM_Frame * ( *clone )( struct GENICAM_Frame * thiz );
```

## 5. reset

> /// \brief      外面不使用时需要调用此接口释放 Image 相关内存

```cpp
void ( *reset )( struct GENICAM_Frame * thiz );
```

## 6. valid

> /// \brief      检查框架是否有效

> /// \retval     < 0 该帧无效

> /// \retval     = 0 该帧有效

```cpp
int32_t( *valid )( struct GENICAM_Frame * thiz );
```

# 二、图片信息

## 7. getImage

> /// \brief      获取该帧图片数据的内存首地址

> /// \return    返回该帧图片数据的内存首地址

```cpp
const void * ( *getImage )( struct GENICAM_Frame * thiz );
```

## 8. getImageWidth

> /// \brief      获取图片宽度

> /// \return    返回图片宽度，失败时为 0

```cpp
uint32_t( *getImageWidth )( struct GENICAM_Frame * thiz );
```

## 9. getImageHeight

> /// \brief      获取图片高度

> /// \return    返回图片高度，失败时为 0

```cpp
uint32_t( *getImageHeight )( struct GENICAM_Frame * thiz );
```

## 10. getImageSize

> /// \brief      获取图片大小

> /// \return    返回图片大小，失败时为 0

```cpp
uint32_t( *getImageSize )( struct GENICAM_Frame * thiz );
```

## 11. getImagePaddingX

> /// \brief      获取图片 paddingX

> /// \return    返回图片 paddingX ，失败时为 0

```cpp
uint32_t( *getImagePaddingX )( struct GENICAM_Frame * thiz );
```

## 12. getImagePaddingY

> /// \brief      获取图片 paddingY

> /// \return    返回图片 paddingY，失败时为 0

```cpp
uint32_t( *getImagePaddingY )( struct GENICAM_Frame * thiz );
```

## 13. getImagePixelFormat

> /// \brief      获取图片像素格式

> /// \return    返回图片像素格式

```cpp
enum EPixelType( *getImagePixelFormat )( struct GENICAM_Frame * thiz );
```

## 14. getImageTimeStamp

> /// \brief      获取图片时间戳

> /// \return    返回图片时间戳，失败时为 0

```cpp
uint64_t( *getImageTimeStamp )( struct GENICAM_Frame * thiz );
```

## 15. getBlockId

> /// \brief      获取图片对应的 BlockId

> /// \return    返回图片对应的 BlockId，失败时为 0

```cpp
uint64_t( *getBlockId )( struct GENICAM_Frame * thiz );
```

# 三、数据帧

## 16. getFrameStatus

> /// \brief      获取数据帧状态

> /// \return    返回数据帧状态

```cpp
uint32_t( *getFrameStatus )( struct GENICAM_Frame * thiz );
```

## 17.  getPayLoadTypes

> /// \brief      获取当前帧数据净荷类型

> /// \param    [in/out] pPayloadTypeList 当前帧数据类型种类集，最大种类不超过
> 
>                     MAX_PAYLOAD_TYPE_CNT，该内存外部分配

> /// \param    [in/out] pTypeCnt 入参时为数组的实际长度；出参时为当前帧数据类型的
> 
>                     实际个数，最大不超过 20

> /// \return     返回当前帧数据净荷类型

```cpp
int32_t( *getPayLoadTypes )( struct GENICAM_Frame * thiz
    , GENICAM_EPayloadType aPayloadTypeList[ MAX_PAYLOAD_TYPE_CNT ]
    , uint32_t * pTypeCnt );
```

# 四、Chunk 数据块

## 18. getChunkCount

> /// \brief      获取 Buffer 中包含的 Chunk 个数

> /// \return    Chunk 个数

```cpp
uint32_t( *getChunkCount )( struct GENICAM_Frame * thiz );
```

## 19. getChunkDataByIndex

> /// \brief      获取Chunk数据

> /// \param    [in] aIndex 索引ID

> /// \return    [out] pID ChunkID

> /// \param    [in/out] aRefParamList Chunk 数据对应的属性名，该内存外部分配，
> 
>                     接口约定 ChunkID 对应的参数列表最大不超过 MAX_PARAM_CNT 个，
> 
>                     每个属性的字符长度不超过 MAX_STRING_LENTH

> /// \param    [in/out] pParamCnt Chunk对应的属性个数，入参时为最大的参数个数，
> 
>                     出参时为实际的参数个数

> /// \return     是否成功

```cpp
int32_t( *getChunkDataByIndex )( struct GENICAM_Frame * thiz
        , uint32_t aIndex
        , uint32_t * pID
        , char aRefParamList[ MAX_PARAM_CNT ][ MAX_STRING_LENTH ]
        , uint32_t * pParamCnt );
```

# 五、保存

## 20. reserved[ 13 ]

> /// \brief      记录保留

```cpp
uint32_t  reserved[ 13 ];
```
