# CryptLib

加密解密静态库组件是一个静态链接库，提供常用的加解密算法。

## 加密解密算法类

### ODes

- DES/3DES **分组**加密解密算法；

- 加密：**以 8 个字节为分组**对数据进行加密，如果源数据的最后一个分组不足8字节，算法通过补 0 的方式将其补足 8 字节，所以加密得到的结果数据（密文）**长度大于源数据**（明文）长度 **0～7 个字节**；
- 解密：将补充的 0 去掉，所以解密得到的结果数据（明文）长度**小于源数据（密文）**长度 0～7 个字节。 

> 1. 算法会根据密钥长度自动选择加密方案：当密钥长度为 8 字节时将使用标准 DES 加密，当密钥长度为 16 字节时，系统将设置第 2 密钥，并启用 3 次 DES 加密；
> 2. 密钥长度必须等于 8 或 16，否则返回失败；
> 3. 算法支持 ECB,CBC,CFB,OFB 四种操作模式的运算，用户在调用时可通过设置参数选择运算的模式。

#### iDoDes

```cpp
int iDoDes(
	BYTE *p_pucResult,
	BYTE *p_pucSrc,
	long p_lSrcLen,
	const BYTE *p_pucKey,
	int p_iKeyLen,
	int p_iMode = ECB,
	bool p_bType = ENCRYPT);
```

- **p_pucResult**：[out] 加密/解密后的结果数，长度大/小于源数据长度 0～7 个字节；

- **p_pucSrc**：[in] 源数据数组 ；

- **p_lSrcLen**：[in] 源数据的长度；

- **p_pucKey**：[in] 密钥；

- **p_iKeyLen**：[in] 密钥长度；

- **p_iMode**：[in] 操作模式（ECB,CBC,CFB,OFB）；

- **p_bType**：[in] 类型（ENCRYPT - 加密，DECRYPT - 解密）；

- > 返回值：成功 0，失败 1。

### ORAS

- 支持 2048 位的 RSA 运算。 

#### iGetKey

```cpp
int iGetKey(
	int p_iBitlen,
	tRSAKey& p_tPublicKey,
	tRSAKey& p_tPrivateKey);

typedef struct
{
	unsigned int modellen ; // 模长
	BYTE modelvalue[Maxkeylen] ; // 模值
	unsigned int keylen ; // 指数长
	BYTE keyvalue[Maxkeylen] ; // 指数值
	BYTE prime[2][Maxkeylen]; // 两个素数
}tRSAKey ;
```

- **p_iBitlen**：[in] 密钥的 bit 长度，必须大于等于 64、小于等于 2048，否则返回失败；

- **p_tPublicKey**：[out] 公钥；

- **p_tPrivateKey**：[out] 私钥；

- > 返回值：成功 0，失败 1。 

#### iDoRSA

- 当 p_tKey 是私钥时为加密，当 p_tKey 是公钥时是解密。 

```cpp
int iDoRSA(
	BYTE p_acResult[],
	unsigned int& p_uiResultLen,
	BYTE p_acSrc[],
	unsigned int p_uiSrcLen,
	tRSAKey p_tKey
	int p_iMode = RSAKEY_COMMANMODE);
```

- **p_acResult**：[out] 加密/解密后的结果数据数组；	

- **p_uiResultLen**：[out] 结果数据的长度；

- **p_acSrc**：[in] 源数据数，不能以字节 0X00 开头；

- **p_uiSrcLen**：[in] 源数据的长度；

- **p_tKey**：[in] 密钥；

- **p_iMode**：[in] 模式：非 5 时，对源数据进行核心运算之前，会对源数据进行一个补充位数的打包过程，为 5 时则不进行任何处理，“不打包方式”；

  | #define RSAPRIVATEKEY_ENCRYPTMODE 1    | 传入的密钥必须为私钥，对应的解密模式为 4     |
  | -------------------------------------- | -------------------------------------------- |
  | #define RSAPRIVATEKEY_DECRYPTMODE 2    | 传入的密钥必须为私钥，对应的加密模式为 3     |
  | #define RSAPUBLICKEY_ENCRYPTMODE 3     | 传入的密钥必须为公钥，对应的解密模式为 2     |
  | **#define RSAPUBLICKEY_DECRYPTMODE 4** | **传入的密钥必须为公钥，对应的加密模式为 1** |
  | #define RSAKEY_COMMANMODE 5            | 加/解密模式为 5                              |

- > 返回值：成功返回0，失败返回1。 

### OCrc

- （循环冗余校验）校验算法。 

#### iGetCRC16 / 32

```cpp
int iGetCRC16 / iGetCRC32(
	BYTE *p_pucSrcData, 
	int p_iSrcSize);
```

- **p_pucSrcData**：[in] 源数据数组；

- **p_iSrcSize**：[in] 源数据的长度，如果小于等于 0，则取源数据数组的字符串长度；

- > 返回值：计算所得的校验数据的值，失败返回 -1。 

### HASH

- 单项散列函数算法，一种将原文进行大象运算得出抽样的算法；
- 只能由原文得出抽样，不能从抽样推导出原文；
- 对相同的单项散列算法，不同的原文只能得出不同的抽样。

#### OSHA1

##### iSHA1_Init

- 初始化 tSHA1_CTX 结构类型的变量；

```cpp
int iSHA1_Init(tSHA1_CTX  *p_ptContext); 
```

- **p_ptContext**：[in、out] tSHA1_CTX 结构类型的变量；

- > 返回值：成功 0，失败返回错误代码。 

##### iSHA1_Update

```cpp
int iSHA1_Update(
    tSHA1_CTX *p_ptContext, 
    const uint8_t  *p_puiData, 
    unsigned p_uiDataLen); 
```

- **p_ptContext**：[in] 源 tSHA1_CTX 结构类型的变量；

- **p_puiData**：[in] 源文件；

- **p_uiDataLen**：[in] 源文件长度；

- >返回值：成功 0，失败返回错误代码。

##### iSHA1_End

- 对 tSHA1_CTX 结构中的数据进行处理，再以字符串 (160 - bit) 的形式输出。 

```cpp
#define SHA1HashSize 20 

int iSHA1_End(
	tSHA1_CTX *p_ptContext,
    uint8_t p_aucResult[SHA1HashSize]);
```

- **p_ptContext**：[in] 源 tSHA256_CTX 结构类型的变量；

- **p_aucResult**：[out] 存储结果的字符数组（串），输出的数据长度为固定值；

- >返回值：成功 0，失败返回错误代码。

#### OSHA2

- 对数据进行 SHA2 算法加密时，用户可以根据需要对该函数进行多次调用。 

##### vSHA256 / 384 / 512_Init

- 初始化 tSHA256/384/512_CTX 结构类型的变量。 

```cpp
void vSHA256_Init(tSHA256_CTX* p_ptContext); 
void vSHA384_Init(tSHA384_CTX*  p_ptContext); 
void vSHA512_Init(tSHA512_CTX* p_ptContext);
```

- **p_ptContext**：[out、in] tSHA256/384/512_CTX 结构类型的变量 

##### vSHA256 / 384 / 512_Update

- 对指定长度（p_uiDataLen）的源文件（p_puiData）与tSHA256 / 384 / 512_CTX(p_ptContext) 结构中的数据，按 SHA2 算法进行计算，并将结果存储在 tSHA256 / 384 / 512_CTX 结构中。

```cpp
void vSHA256_Update(tSHA256_CTX* p_ptContext, const u_int8_t* p_puiData, size_t p_uiDataLen);
void vSHA384_Update(tSHA384_CTX* p_ptContext, const u_int8_t* p_puiData, size_t p_uiDataLen);
void vSHA512_Update(tSHA512_CTX* p_ptContext, const u_int8_t* p_puiData, size_t p_uiDataLen);
```

- **p_ptContext**：[in、out ] 源 tSHA256 / 384 / 512_CTX 结构类型的变量；
- **p_puiData**：[in] 源文件；
- **p_uiDataLen**：[in] 源文件长度。

##### vSHA256 / 384 / 512_End

- 对 tSHA256 / 384 / 512_CTX 结构中的数据进行处理，再以字符串(固定长度)的形式输出。 

```cpp
#define SHA256_DIGEST_BLOCKLEN 64 
#define SHA256_DIGEST_VALUELEN 32 
#define SHA384_DIGEST_BLOCKLEN 128 
#define SHA384_DIGEST_VALUELEN 48 
#define SHA512_DIGEST_BLOCKLEN 128 
#define SHA512_DIGEST_VALUELEN 64 

void vSHA256_End(tSHA256_CTX* p_ptContext, uint8_t  p_aucResult[SHA256_DIGEST_VALUELEN]); 
void vSHA384_End(tSHA384_CTX*  p_ptContext, uint8_t p_aucResult[SHA384_DIGEST_VALUELEN]); 
void vSHA512_End(tSHA512_CTX* p_ptContext, uint8_t  p_aucResult[SHA512_DIGEST_VALUELEN]);  
```

- **p_ptContext**：[in] 源 tSHA256 / 384 / 512_CTX 结构类型的变量；
- **p_aucResult**：[out] 存储结果的字符数组（串）。输出的数据长度为固定值；

#### OMD5

##### iDoMD5

```cpp
int iDoMD5(
	BYTE p_aucResult[16], 
	BYTE *p_pucSrc, 
	int p_iSrcLen);
```

- **p_aucResult**：[out] 结果数据数组（16字节）；

- **p_pucSrc**：[in] 源数据数组；

- **p_iSrcLen**：[in] 源数据的长度，如果小于等于 0，则取源数据数组的字符串长度；

- > 返回值：成功返回0，失败返回1。 

## 随机数生成算法函数

### iGetRandIntArray

- 在一个数值范围（0 ~ p_iMax）之间，生成有限个一系列非重复的整型随机数。 

```cpp
int iGetRandIntArray(
	int p_aiResult[],
	int p_iNum,
	int p_iMax);
```

- **p_aiResult**：[out] 存储随机数的整型数组；

- **p_iNum**：[in] 随机数的个数；

- **p_iMax**：[in] 随机数的最大值；

- > 返回值：成功 0，失败 1。

### iGetRandCharArray

- 生成有限个一系列非重复的字符型随机数，ASCII 值在 0～7F 之间。 

```cpp
int  iGetRandIntArray(BYTE p_aucResult[], int p_iNum);  
```

- **p_aucResult**：[out] 存储随机数的字符型数组；

- **p_iNum**：[in] 随机数的个数，最大值为 127，大于 127 直接返回失败；

- > 返回值：成功 0，失败 1。

## MAC 校验数据生成算法函数

### iGetTranMacValue

- 生成 8 位 MAC 校验数据。 

```cpp
int iGetTranMacValue(BYTE *p_pcMAC, BYTE *p_pcSource, int p_iSrcLen, BYTE *p_pcKey, int p_iKenLen = -1, int p_iDesMode = 1); 
```

- **p_pcMAC**：[out] 存储结果的字符型数组；
- **p_pcSource**：[in] 源数据；
- **p_iSrcLen**：[in] 源数据的长度；
- **p_pcKey**：[in] 密钥（长度要求等于8或16）；
- **p_iKenLen**：[in] 输入密钥的长度（长度等于 8 时进行 DES 计算，等于 16 时进行 3DES 计算，等于其他值函数返回失败）；
- **p_iDesMode**：[in] 进行 DES 计算时的操作模式（0-ECB,  1-CBC, 2-CFB, 3-OFB），默认为 CBC。