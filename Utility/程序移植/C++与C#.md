# 移植原因

## C++ ~ C#

1. C# 跟 C++ 处理不同，指针不能传在于 C# 封装了指针概念，只有变量和普通数据类型，所以 C# 不需要人工管理内存，也是出于自动内存管理而创建了对指针更高级的内存管理机制使得封装；
2. 处理的数据如果不是变量和普通数据类型时，如指针、结构体数组地址，像这样带有连续内存布局但是传递的是一个指向该内存的区域时，C# 需要有个替代，而用的是特殊数据类型：内存块 Intptr（用于表示指针或句柄的平台相关整数）；
3. 先是申请 Intptr：即内存大小，以复杂结构体为例， 
   - 对结构体内部，结构体成员需要转换对应的数据类型，使得结构体（成员）大小等同于结构体（数组）（成员）大小；
   - 对结构体外部（整体），需要处理控制编码（字符串转换）、内存布局（1 字节同时也是连续布局）还有字节对齐（1字节）；
4. 再是指向，因为申请到了内存之后，要按一定的排布拿出数据，于是有了内存块与结构体的转换，所以就没法直接在 Intptr 身上拿
5. 最后是释放，保持内存块的申请与释放。

# 一、数据模型

## 1.1 数据兼容

### ILP32

32 位数据模型 int、long、指针（p）都是 32 位的

### LP646

64 位数据模型 long、指针（p）都是 64 位的

### 基础数据

| 数据类型  | 数据长度（32位环境） | 数据长度（64位环境） | 是否signed |
| --------- | -------------------- | -------------------- | ---------- |
| ``char``  | 8                    | 8                    | Y          |
| ``short`` | 16                   | 16                   | Y          |
| ``int``   | 32                   | 32                   | Y          |
| long      | **32**               | 64                   | Y          |
| long long | 64                   | 64                   | Y          |
| point     | **32**               | 64                   | N          |
| size_t    | **32**               | 64                   | N          |
| ssize_t   | **32**               | 64                   | Y          |
| off_t     | **32**               | 64                   | Y          |

## 1.2 异常变化

### 数据打印

```cpp
size_t val = xxx;
printf("%zu",val);
// size_t 出于环境不同，打印无法使用 ld 或 d 等
```

### 数据截断

数据长度变化，如精度缩小、长度变短；

### 结构体内存对齐问题

- 结构体变量长度在不同环境中发生变化；
- 内存对齐不一致；
- 变量先后不一致。

## 1.3 定长数据类型

- 头文件 <cstdint>
- 固定长度的数据类型有：

| 数据类型      | 定义描述        | 字节数大小 |
| ------------- | --------------- | ---------- |
| std::int8_t   | 8 位有符号整数  | 1          |
| std::uint8_t  | 8 位无符号整数  | 1          |
| std::int16_t  | 16 位有符号整数 | 2          |
| std::uint16_t | 16 位无符号整数 | 2          |
| std::int32_t  | 32 位有符号整数 | 4          |
| std::uint32_t | 32 位无符号整数 | 4          |
| std::int64_t  | 64 位有符号整数 | 8          |
| std::uint64_t | 64 位无符号整数 | 8          |

- 固定宽度的数据类型有：
  - **std::int_least16_t** ：16 位有符号整数；
  - **std::uint_least16_t** ：16 位无符号整数。

### 数据打印

```cpp
#include <inttypes.h> // 格式化字符串，包含了 stdint.h
uint32_t num=10;
printf("uint32: %" PRIu32 "\n", num); // 为了兼容打印时采用 PRId64/PRIu64 宏
int64_t num1=10;
printf("int64_t: %"PRId64"\n", num1);
```

# 二、C++ 与 C#

[最详细的C++对应C#的数据类型转换 - Innershar - 博客园 (cnblogs.com)](https://www.cnblogs.com/innershare/p/10594775.html)

## 2.1 基本数据类型

| 常用数据类型（C++）                                          | C#                     |
| ------------------------------------------------------------ | ---------------------- |
| HWND、**HANDLE**(void  *)、LDWORD、LLONG、**void ***、UCHAR * | System.**IntPtr**      |
| Byte(**unsigned  char**)                                     | System.Byte            |
| SHORT(**short**)、INT(**short int**)                         | System.Int16           |
| INT(long int)、LONG(**long**)                                | System.Int32           |
| bool                                                         | bool                   |
| CHAR(char)                                                   | System.Char            |
| LPSTR(**char  ***)、LPWSTR(**wchar_t  ***)、LPCSTR(**const  char  ***)、LPCWSTR(**const  wchar_t  ***)、UCHAR * | System.String          |
| FLOAT(float)                                                 | System.Single（float） |
| DOUBLE(double)                                               | System.Double          |
| WORD                                                         | ushort                 |
| GUID                                                         | Guid                   |
| DWORD、UCHAR                                                 | int                    |

## 2.2 字符、字符串

| 变量形式 C++          | C#                                                           |
| --------------------- | ------------------------------------------------------------ |
| char 变量名           | byte 变量名（两个字节表示 C++ 一个字节）                     |
| char 数组名[数组大小] | MarshalAs(UnmanagedType.ByValTStr, SizeConst = 数组大小)] public string 数组名 |
| char *                | string，传入参数                                             |
| char *                | StringBuilder，传出参数                                      |
| char * 变量名         | ref string 变量名                                            |
| char * 输入变量名     | string 输入变量名                                            |
| char * 输出变量名     | [MarshalAs(UnmanagedType.LPStr)] StringBuilder 输出变量名    |
| char **、char[ ]      | string                                                       |
| char ** 变量名        | ref string 变量名                                            |

## 2.3 宏定义

C++

```cpp
// 常量
#define W_NOSUPPORT_FUNCTION 398

// 方法函数
#define LOWORD(l)           ((WORD)(((DWORD_PTR)(l)) & 0xffff))
```

### 2.3.1 常量

- 不使用 #define 定义；
- 使用 **const** 定义。

```C#
namespace GRGCDMDev.Define
{
    public static class GRGCDMDef
    {
        public const int W_NOSUPPORT_FUNCTION = 398;	// 不支持此功能
    }
}
```

### 2.3.2 泛型方法

- 使用 internal 定义 static 静态方法；
- Func<object, object>；

```C#
namespace System
{
    public static class Def
    {
        internal static Func<object, object> LOWORD = (l) => ((ushort)(((ulong)(l)) & 0xffff));
    }
}
```

## 2.4 结构体

- C++ 结构体需要在 C# 中重新定义；
- 字节对齐；
- 字符串编码格式需一致；

### 2.4.1 成员类型对应表

| C++                                            | C#                                                  |
| ---------------------------------------------- | --------------------------------------------------- |
| int *、int &                                   | ref int                                             |
| 双指针                                         | ref IntPtr                                          |
| 函数指针：typedef double (*fun_type1)(double); | 委托：public delegate double fun_type1(double);     |
| char *、LPCTSTR                                | StringBuilder、string                               |
| unsigned  char                                 | byte                                                |
| DWORD、ulong、unsigned int                     | uint                                                |
| UCHAR *                                        | string / IntPtr                                     |
| unsigned char *                                | [MarshalAs(UnmanagedType.LPArray)] byte[ ] 、Intptr |
| LPTSTR                                         | [MarshalAs(UnmanagedType.LPTStr)] string            |
| long                                           | int                                                 |
| Handle、HWND、void *                           | IntPtr                                              |

### 2.4.2 对齐与编码

```C#
using System.Runtime.InteropServices;

    [StructLayout(LayoutKind.Sequential, CharSet = CharSet.Ansi, Pack = 1)]
    public struct tDevReturn
    {
        public int iLogicCode;		// 逻辑错误码
        public int iPhyCode;			// 物理错误码
        public int iHandle;			// 处理方法：0-不处理 1-初始化 2-重发命令 3-停机修复 4-软件或硬件重新配置		
        public int iType;			// 错误类型：0-正常码 1警告码 2-操作错 3-传输错 4-设备错 5-超时错

        [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 128)]
        public string acDevReturn;  // 硬件返回信息
        [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 128)]
        public string acReserve;		// 保留信息
    };
```

### 2.4.3 初始化

C++

```c++
tDevReturn p_psStatus[8] = {0};
memset(p_psStatus, 0x00, sizeof(tDevReturn));
```

C#

```C#
// 命名空间
using System.Runtime.InteropServices;

// 初始化结构体数组
tDevReturn[] l_asStatus = new tDevReturn[8];
int l_size = Marshal.SizeOf(typeof(tDevReturn)) * l_asStatus.Length;
IntPtr l_pBuff = Marshal.AllocHGlobal(l_size); // 创建有结构体数组长度大小的特殊数据类型：指针

// 将结构体拷贝到指针内存块中
try
{
    for (int i = 0; i < 8; i++)
    {
        IntPtr l_prStruct = IntPtr.Add(l_pBuff, Marshal.SizeOf(typeof(tDevReturn)) * i);
        Marshal.StructureToPtr(l_asStatus[i], l_prStruct, false); // 结构体 to Ptr
    }
    
    // 从内存块中取回修改后的结构体数组数据
    for (int i = 0; i < 8; i++)
    {
        IntPtr l_prStruct = IntPtr.Add(p_tDevReturn, Marshal.SizeOf(typeof(tDevReturn)) * i);
        l_asStatus[i] = (tDevReturn)Marshal.PtrToStructure(l_prStruct, typeof(tDevReturn));
    }
    
    // 使用
    int a = l_asStatus[8].iHandle;
}
finally
{
    Marshal.FreeHGlobal(l_pBuff);
}
```

### 2.4.4 示例

C++

```cpp
typedef struct
{
	char				acORCInfo[40];
	PBYTE				byImageBuffer;
	UINT				uiBuferrLen;
	t6240NCashInfo		 sCIMCashInfo;
	char				acPathName[MAX_PATH];
}t6240NOCRInfo;
```

C#

```C#
    [StructLayout(LayoutKind.Sequential, CharSet = CharSet.Ansi, Pack = 1)]
    struct t6240NOCRInfo
    {
        [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 40)]
        public string acORCInfo;

        public IntPtr byImageBuffer;
        public UInt32 uiBuferrLen;

        public t6240NCashInfo sCIMCashInfo;

        [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 260)]
        public string acPathName;
    };
```

## 2.5 接口函数成员

- dll 导入：[**DllImport**("GRGCDMDev.dll", **EntryPoint** = "CDM_iSetCommPara", CallingConvention = CallingConvention.Cdecl, **CharSet** = CharSet.Ansi)]；
- 函数定义：纯 C 导入，public static extern 返回类型 函数名称(成员变量)；
- 成员变量为**结构体指针**，使用 **Intptr**；

### 2.5.1 成员类型对应表

| C++                                              | C#             |
| ------------------------------------------------ | -------------- |
| HWND、LPARAM、HINSTANCE、HANDLE、LRESULT、WPARAM | System.IntPtr  |
| SHORT、USHORT、WORD                              | System.Int16   |
| BOOL、INT、INT32                                 | System.Int32   |
| SIZE_T                                           | System.UInt32  |
| INT64                                            | System.Int64   |
| ULONG64、ULONGLONG                               | System.UInt64  |
| LPST、LPTSTR                                     | System.String  |
| PCHAR                                            | System.Char[ ] |
| UCHAR                                            | System.Byte    |
| TCHAR、TBYTE                                     | System.Char    |

### 2.5.2 示例

C++

```cpp
int WINAPI CDM_iGetCassetteInfo(BYTE p_byHopper, tGRGCDMCassetteInfo *p_psCassetteInfo, BYTE& p_bySize, tDevReturn *p_psStatus);
```

C#

```C#
[DllImport("GRGCDMDev.dll", EntryPoint = "CDM_iGetCassetteInfo", CallingConvention = CallingConvention.Cdecl, CharSet = CharSet.Ansi)]
public static extern int iGetCassetteInfo(byte p_byHopper, IntPtr p_tGRGCDMCassetteInfo, ref byte p_bySize, IntPtr p_tDevReturn);
```

