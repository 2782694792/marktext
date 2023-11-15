# <assert.h>

## 简介

- #include<assert.h> 提供了一个名为 assert 的宏；

- 可用于验证程序做出的假设，并在假设为假时输出诊断消息；

- 已定义的宏 **assert** 指向另一个宏 **NDEBUG**，宏 **NDEBUG** 不是 <assert.h> 的一部分；

- 如果已在引用 <assert.h> 的源文件中定义 NDEBUG 为宏名称，则 **assert** 宏的定义如下：

```cpp
#define assert(ignore) ((void)0)
```

## 库宏

实际上是一个宏，不是一个函数，可用于在 C 程序中添加诊断。

```cpp
void assert(int expression)
```

# <ctype.h>

## 简介

- 可用于测试和映射字符；

- 接收 int 作为参数，它的值必须是 EOF 或表示为一个无符号字符；

- 如果参数 c 满足描述条件，则返回非零（true）。如果参数 c 不满足描述条件，则返回零。

## 库函数

### 检查类型

| int isalnum(int c)  | 检查所传的字符是否是字母和数字 |
| ------------------- | --------------- |
| int isalpha(int c)  | 检查所传的字符是否是字母    |
| int iscntrl(int c)  | 是否是控制字符         |
| int isdigit(int c)  | 是否是十进制数字        |
| int isgraph(int c)  | 是否有图形表示法        |
| int islower(int c)  | 是否是小写字母         |
| int isprint(int c)  | 是否是可打印的         |
| int ispunct(int c)  | 是否是标点符号字符       |
| int isspace(int c)  | 是否是空白字符         |
| int isupper(int c)  | 是否是大写字母         |
| int isxdigit(int c) | 是否是十六进制数字       |

### 大小写转换

接受并返回一个 "int"

| int tolower(int c) | 把大写字母转换为小写字母 |
| ------------------ | ------------ |
| int toupper(int c) | 把小写字母转换为大写字母 |

### 字符类

| 标点符号字符 | 集合 ! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { \| } ~ |
| ------ | ------------------------------------------------------------------- |
| 图形字符   | 字母数字字符和标点符号字符的集合                                                    |
| 控制字符   | ASCII 编码中，这些字符的八进制代码是从 000 到 037，以及 177（DEL）                        |

# <errno.h>

## 简介

- 定义了整数变量 **errno**，它是通过系统调用设置的；

- 在错误事件中的某些库函数表明了什么发生了错误；

- 该宏扩展为类型为 int 的可更改的左值，因此它可以被一个程序读取和修改；

- 在程序启动时，**errno** 设置为零，C 标准库中的特定函数修改它的值为一些非零值以表示某些类型的错误。

## 库函数

| extern int errno   | 通过系统调用设置的宏，在错误事件中的某些库函数表明了什么发生了错误               |
| ------------------ | ----------------------------------------------- |
| EDOM Domain Error  | 表示一个域错误，它在输入参数超出数学函数定义的域时发生，errno 被设置为 EDOM     |
| ERANGE Range Error | 表示一个范围错误，它在输入参数超出数学函数定义的范围时发生，errno 被设置为 ERANGE |

# <float.h>

## 简介

- 头文件包含了一组与浮点值相关的依赖于平台的常量；

- 浮点数是由下面四个元素组成的：

| S   | 符号 ( +/- )                               |
| --- | ---------------------------------------- |
| b   | 指数表示的基数，2 表示二进制，10 表示十进制，16 表示十六进制，等等... |
| e   | 指数，一个介于最小值 **emin** 和最大值 **emax** 之间的整数。 |
| p   | 精度，基数 b 的有效位数                            |

- 一个浮点数的值如下：

![](D:\Download\MarkText\workspace\image\2022-10-22-16-42-30-image.png)

## 库宏

下面的值是特定实现的，通过 #define 来定义，这些值都不得低于下边所给出的值：

> **FLT** 是指类型 **float**，**DBL** 是指类型 **double**，**LDBL** 是指类型 **long double**。

| FLT_ROUNDS                                              | 定义浮点加法的舍入模式，它可以是下列任何一个值：<br><br>-1 - 无法确定<br>0 - 趋向于零<br>1 - 去最近的值<br>2 - 趋向于正无穷3 - 趋向于负无穷 |
| ------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| FLT_RADIX 2                                             | 定义了指数表示的基数。基数 2 表示二进制                                                                      |
| FLT_MANT_DIGDBL_MANT_DIGLDBL_MANT_DIG                   | 定义了 FLT_RADIX 基数中的位数                                                                       |
| FLT_DIG 6DBL_DIG 10LDBL_DIG 10                          | 定义了舍入后不会改变表示的十进制数字的最大值（基数 10）                                                              |
| FLT_MIN_EXPDBL_MIN_EXPLDBL_MIN_EXP                      | 定义了基数为 FLT_RADIX 时的指数的最小负整数值                                                               |
| FLT_MIN_10_EXP -37DBL_MIN_10_EXP -37LDBL_MIN_10_EXP -37 | 定义了基数为 10 时的指数的最小负整数值                                                                      |
| FLT_MAX_EXPDBL_MAX_EXPLDBL_MAX_EXP                      | 定义了基数为 FLT_RADIX 时的指数的最大整数值                                                                |
| FLT_MAX_10_EXP +37DBL_MAX_10_EXP +37LDBL_MAX_10_EXP +37 | 定义了基数为 10 时的指数的最大整数值                                                                       |
| FLT_MAX 1E+37DBL_MAX 1E+37LDBL_MAX 1E+37                | 定义最大的有限浮点值                                                                                 |
| FLT_EPSILON 1E-5DBL_EPSILON 1E-9LDBL_EPSILON 1E-9       | 定义了可表示的最小有效数字                                                                              |
| FLT_MIN 1E-37DBL_MIN 1E-37LDBL_MIN 1E-37                | 定义了最小的浮点值                                                                                  |

# <limits.h>

## 简介

- **limits.h** 头文件决定了各种变量类型的各种属性；

- 定义在该头文件中的宏限制了各种变量类型（比如 char、int 和 long）的值，例如一个无符号可以存储的最大值是 255。

## 库宏

下面的值是特定实现的，且是通过 #define 指令来定义的，这些值都不得低于下边所给出的值：

| 宏          | 值                    | 描述                                                   |
| ---------- | -------------------- | ---------------------------------------------------- |
| CHAR_BIT   | 8                    | 一个字节的比特数                                             |
| SCHAR_MIN  | -128                 | 一个有符号字符的最小值                                          |
| SCHAR_MAX  | 127                  | 一个有符号字符的最大值                                          |
| UCHAR_MAX  | 255                  | 一个无符号字符的最大值                                          |
| CHAR_MIN   | 0                    | 类型 char 的最小值，如果 char 表示负值，则等于 SCHAR_MIN，否则等于 0       |
| CHAR_MAX   | 127                  | 类型 char 的最大值，如果 char 表示负值，则等于 SCHAR_MAX，否则 UCHAR_MAX |
| MB_LEN_MAX | 1                    | 多字节字符中的最大字节数                                         |
| SHRT_MIN   | -32768               | 一个短整型的最小值                                            |
| SHRT_MAX   | +32767               | 一个短整型的最大值                                            |
| USHRT_MAX  | 65535                | 一个无符号短整型的最大值                                         |
| INT_MIN    | -2147483648          | 一个整型的最小值                                             |
| INT_MAX    | 2147483647           | 一个整型的最大值                                             |
| UINT_MAX   | 4294967295           | 一个无符号整型的最大值                                          |
| LONG_MIN   | -9223372036854775808 | 一个长整型的最小值                                            |
| LONG_MAX   | 9223372036854775807  | 一个长整型的最大值                                            |
| ULONG_MAX  | 1.8446744e+19        | 一个无符号长整型的最大值                                         |

# <locale.h>

## 简介

- **locale.h** 头文件定义了特定地域的设置，比如日期格式和货币符号。

## 库宏

| **LC_ALL**      | 设置下面的所有选项                    |
| --------------- | ---------------------------- |
| **LC_COLLATE**  | 影响 strcoll 和 strxfrm 函数      |
| **LC_CTYPE**    | 影响所有字符函数                     |
| **LC_MONETARY** | 影响 localeconv 函数提供的货币信息      |
| **LC_NUMERIC**  | 影响 localeconv 函数提供的小数点格式化和信息 |
| **LC_TIME**     | 影响 strftime 函数               |

## 库函数

| **char *setlocale(int category, const char *locale** | 设置或读取地域化信息。 |
| ---------------------------------------------------- | ----------- |
| **struct lconv *localeconv(void)**                   | 设置或读取地域化信息。 |

## 库结构

```cpp
typedef struct {
   char *decimal_point;     // 非货币值的小数点字符
   char *thousands_sep;     // 非货币值的千位分隔符
   char *grouping;          // 一个表示非货币量中每组数字大小的字符串。
                            //    每个字符代表一个整数，每个整数指定当前组位数，
                            //    值为 0 ：前一个值将应用于剩余分组
   char *int_curr_symbol;   // 国际货币符号使用的字符串。
                            //    前三个字符由 ISO 4217:1987 指定的，
                            //    第四个字符用于分隔货币符号和货币量
   char *currency_symbol;   // 货币的本地符号
   char *mon_decimal_point; // 货币值的小数点字符
   char *mon_thousands_sep; // 货币值的千位分隔符
   char *mon_grouping;      // 一个表示货币值中每组数字大小的字符串
   char *positive_sign;     // 正货币值的字符
   char *negative_sign;     // 负货币值的字符
   char int_frac_digits;    // 国际货币值中小数点后要显示的位数
   char frac_digits;        // 货币值中小数点后要显示的位数
   char p_cs_precedes;      // 等于 1，则 currency_symbol 出现在正货币值之前；
                            //     等于 0，则正货币值之后
   char p_sep_by_space;     // 为 1，currency_symbol 和正货币值之间空格分隔；
                            //     为 0，则不使用空格分隔
   char n_cs_precedes;      // 等于 1，则 currency_symbol 出现在负货币值之前；
                            //     等于 0，则负货币值之后
   char n_sep_by_space;     // 为 1，currency_symbol 和负货币值之间空格分隔；
                            //     为 0，则不使用空格分隔
   char p_sign_posn;        // 正货币值中正号的位置
   char n_sign_posn;        // 负货币值中负号的位置
} lconv;
```

下面的值用于 **p_sign_posn** 和 **n_sign_posn**:

| 值   | 描述                             |
| --- | ------------------------------ |
| 0   | 封装值和 currency_symbol 的括号       |
| 1   | 放置在值和 currency_symbol 之前的符号    |
| 2   | 放置在值和 currency_symbol 之后的符号    |
| 3   | 紧挨着放置在值和 currency_symbol 之前的符号 |
| 4   | 紧挨着放置在值和 currency_symbol 之后的符号 |

# <math.h>

## 简介

- **math.h** 头文件定义了各种数学函数和一个宏。在这个库中所有可用的功能都带有一个 **double** 类型的参数，且都返回 **double** 类型的结果。

## 库宏

| **HUGE_VAL** | 当函数的结果不可以表示为浮点数时。<br/>如果因为结果幅度太大以致无法表示，则函数会设置 errno 为 ERANGE 来表示范围错误，并返回一个由宏 HUGE_VAL 或者否定（- HUGE_VAL）命名的一个特定的很大的值。<br/>如果结果幅度太小，则返回零值。在这种情况下，error 可能会被设置为 ERANGE，也有可能不会被设置为 ERANGE。 |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

## 库函数

| acos( double )                   | 返回以弧度表示的 x 的反余弦                                                             |
| -------------------------------- | --------------------------------------------------------------------------- |
| asin( double )                   | 返回以弧度表示的 x 的反正弦                                                             |
| atan( double )                   | 返回以弧度表示的 x 的反正切                                                             |
| atan2( double, double )          | 返回以弧度表示的 y/x 的反正切。y 和 x 的值的符号决定了正确的象限                                       |
| cos( double )                    | 返回弧度角 x 的余弦                                                                 |
| cosh( double )                   | 返回 x 的双曲余弦                                                                  |
| sin( double )                    | 返回弧度角 x 的正弦                                                                 |
| sinh( double )                   | 返回 x 的双曲正弦                                                                  |
| tanh( double )                   | 返回 x 的双曲正切                                                                  |
| exp( double )                    | 返回 e 的 x 次幂的值                                                               |
| frexp(double x, int * exponent)  | 把浮点数 x 分解成尾数和指数。<br/>返回值尾数，将指数存入 exponent 中。所得值 x = mantissa * 2 ^ exponent |
| ldexp(double x, int exponent)    | 返回 x 乘以 2 的 exponent 次幂                                                     |
| log( double )                    | 返回 x 的自然对数（基数为 e 的对数）                                                       |
| log10( double )                  | 返回 x 的常用对数（基数为 10 的对数）                                                      |
| modf(double x, double * integer) | 返回值为小数部分（小数点后的部分），并设置 integer 为整数部分                                         |
| pow(double x, double y)          | 返回 x 的 y 次幂                                                                 |
| sqrt( double )                   | 返回 x 的平方根                                                                   |
| ceil( double )                   | 返回大于或等于 x 的最小的整数值                                                           |
| fabs( double )                   | 返回 x 的绝对值                                                                   |
| floor( double )                  | 返回小于或等于 x 的最大的整数值                                                           |
| fmod(double x, double y)         | 返回 x 除以 y 的余数                                                               |

# <setjmp.h>

- 定义了宏 **setjmp()**、函数 **longjmp()** 和变量类型 **jmp_buf**，该变量类型会绕过正常的函数调用和返回规则。

## 库宏

| int setjmp(jmp_buf environment) | 把当前环境保存在变量 **environment** 中，以便函数 **longjmp()** 后续使用。<br/>如果这个宏直接从宏调用中返回，则返回零，但是如果它从 **longjmp()** 函数调用中返回，则返回一个非零值。 |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------- |

## 库函数

| void longjmp( jmp_buf environment, int value ) | 恢复最近一次调用 **setjmp( )** 宏时保存的环境，**jmp_buf** 参数的设置是由之前调用 setjmp() 生成的。 |
| ---------------------------------------------- | -------------------------------------------------------------------- |

## 库变量

| **jmp_buf** | 一个用于存储宏 **setjmp( )** 和函数 **longjmp( )** 相关信息的数组类型 |
| ----------- | -------------------------------------------------- |

# <signal.h>

## 简介

- 定义了一个变量类型 **sig_atomic_t**、两个函数调用和一些宏来处理程序执行期间报告的不同信号。

## 库变量

| **sig_atomic_t** | **int** 类型，在信号处理程序中作为变量使用。<br/>它是一个对象的整数类型，该对象可以作为一个原子实体访问，即使存在异步信号时，该对象可以作为一个原子实体访问。 |
| ---------------- | ------------------------------------------------------------------------------------- |

## 库宏

- **SIG_** 宏与 signal 函数一起使用来定义信号的功能：

| **SIG_DFL** | 默认的信号处理程序。 |
| ----------- | ---------- |
| **SIG_ERR** | 表示一个信号错误。  |
| **SIG_IGN** | 忽视信号。      |

- **SIG** 宏用于表示以下各种条件的信号码：

| **SIGABRT** | 程序异常终止。              |
| ----------- | -------------------- |
| **SIGFPE**  | 算术运算出错，如除数为 0 或溢出。   |
| **SIGILL**  | 非法函数映象，如非法指令。        |
| **SIGINT**  | 中断信号，如 ctrl-C。       |
| **SIGSEGV** | 非法访问存储器，如访问不存在的内存单元。 |
| **GTERM**   | 发送给本程序的终止请求信号。       |

## 库函数

| 序号                                                  | 函数 & 描述                         |
| --------------------------------------------------- | ------------------------------- |
| void( * signal( int sig, void(* func)(int) ) )(int) | 设置一个函数来处理信号，即信号处理程序。            |
| int raise(int sig)                                  | 促使生成信号 **sig**。sig 参数与 SIG 宏兼容。 |

# <stdarg.h>

- 定义了一个变量类型 **va_list** 和三个宏，这三个宏可用于在参数个数未知（即参数个数可变）时获取函数中的参数；

- 可变参数的函数通在参数列表的末尾是使用省略号(,...)定义的。

## 库变量

| **va_list** | 一个适用于 **va_start()、va_arg()** 和 **va_end()** 这三个宏存储信息的类型。 |
| ----------- | --------------------------------------------------------- |

## 库宏

| 序号                                  | 宏 & 描述                                                                                      |
| ----------------------------------- | ------------------------------------------------------------------------------------------- |
| void va_start(va_list ap, last_arg) | 初始化 **ap** 变量，它与 **va_arg** 和 **va_end** 宏是一起使用的。**last_arg** 是最后一个传递给函数的已知的固定参数，即省略号之前的参数。 |
| type va_arg(va_list ap, type)       | 检索函数参数列表中类型为 **type** 的下一个参数。                                                               |
| void va_end(va_list ap)             | 允许使用了 **va_start** 宏的带有可变参数的函数返回。如果在从函数返回之前没有调用 **va_end**，则结果为未定义。                         |

# <stddef.h>

- 定义了各种变量类型和宏。这些定义中的大部分也出现在其它头文件中。

## 库变量

| **ptrdiff_t** | 这是有符号整数类型，它是两个指针相减的结果。          |
| ------------- | ------------------------------- |
| **size_t**    | 这是无符号整数类型，它是 **sizeof** 关键字的结果。 |
| **wchar_t**   | 这是一个宽字符常量大小的整数类型。               |

## 库宏

| NULL                                | 一个空指针常量的值                                                                                    |
| ----------------------------------- | -------------------------------------------------------------------------------------------- |
| offsetof( type, member-designator ) | 生成一个类型为 size_t 的整型常量，它是一个结构成员相对于结构开头的字节偏移量。成员是由 *member-designator* 给定的，结构的名称是在 *type* 中给定的。 |

# <stdio.h>

## 简介

- 定义了三个变量类型、一些宏和各种函数来执行输入和输出。

## 库变量

| 序号         | 变量 & 描述                     |
| ---------- | --------------------------- |
| **size_t** | 无符号整数类型，是 **sizeof** 关键字的结果 |
| **FILE**   | 适合存储文件流信息的对象类型              |
| **fpos_t** | 适合存储文件中任何位置的对象类型            |

## 库宏

| **NULL**                             | 空指针常量的值                                        |
| ------------------------------------ | ---------------------------------------------- |
| **_IOFBF、_IOLBF** 和 **_IONBF**       | 扩展了带有特定值的整型常量表达式，并适用于 **setvbuf** 函数的第三个参数     |
| **BUFSIZ**                           | 整数，代表了 **setbuf** 函数使用的缓冲区大小                   |
| **EOF**                              | 表示已经到达文件结束的负整数                                 |
| **FOPEN_MAX**                        | 整数，代表了系统可以同时打开的文件数量                            |
| **FILENAME_MAX**                     | 整数，代表了字符数组可以存储的文件名的最大长度。如果实现没有任何限制，则该值应为推荐的最大值 |
| **L_tmpnam**                         | 整数，代表了字符数组可以存储的由 tmpnam 函数创建的临时文件名的最大长度        |
| **SEEK_CUR、SEEK_END** 和 **SEEK_SET** | 在 **fseek** 函数中使用，用于在一个文件中定位不同的位置              |
| **TMP_MAX**                          | 是 tmpnam 函数可生成的独特文件名的最大数量                      |
| **stderr、stdin** 和 **stdout**        | 指向 FILE 类型的指针，分别对应于标准错误、标准输入和标准输出流。            |

## 库函数

| 1   | [int fclose(FILE *stream)](https://www.runoob.com/cprogramming/c-function-fclose.html)  <br>关闭流 stream。刷新所有的缓冲区。                                                                                       |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 2   | [void clearerr(FILE *stream)](https://www.runoob.com/cprogramming/c-function-clearerr.html)<br>清除给定流 stream 的文件结束和错误标识符。                                                                               |
| 3   | [int feof(FILE *stream)](https://www.runoob.com/cprogramming/c-function-feof.html)<br>测试给定流 stream 的文件结束标识符。                                                                                           |
| 4   | [int ferror(FILE *stream)](https://www.runoob.com/cprogramming/c-function-ferror.html)<br>测试给定流 stream 的错误标识符。                                                                                         |
| 5   | [int fflush(FILE *stream)](https://www.runoob.com/cprogramming/c-function-fflush.html)<br>刷新流 stream 的输出缓冲区。                                                                                           |
| 6   | [int fgetpos(FILE *stream, fpos_t *pos)](https://www.runoob.com/cprogramming/c-function-fgetpos.html)<br>获取流 stream 的当前文件位置，并把它写入到 pos。                                                                |
| 7   | [FILE *fopen(const char *filename, const char *mode)](https://www.runoob.com/cprogramming/c-function-fopen.html)<br>使用给定的模式 mode 打开 filename 所指向的文件。                                                   |
| 8   | [size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fread.html)<br>从给定流 stream 读取数据到 ptr 所指向的数组中。                                        |
| 9   | [FILE *freopen(const char *filename, const char *mode, FILE *stream)](https://www.runoob.com/cprogramming/c-function-freopen.html)<br>把一个新的文件名 filename 与给定的打开的流 stream 关联，同时关闭流中的旧文件。                 |
| 10  | [int fseek(FILE *stream, long int offset, int whence)](https://www.runoob.com/cprogramming/c-function-fseek.html)<br>设置流 stream 的文件位置为给定的偏移 offset，参数 *offset* 意味着从给定的 *whence* 位置查找的字节数。              |
| 11  | [int fsetpos(FILE *stream, const fpos_t *pos)](https://www.runoob.com/cprogramming/c-function-fsetpos.html)<br>设置给定流 stream 的文件位置为给定的位置。参数 *pos* 是由函数 fgetpos 给定的位置。                                   |
| 12  | [long int ftell(FILE *stream)](https://www.runoob.com/cprogramming/c-function-ftell.html)<br>返回给定流 stream 的当前文件位置。                                                                                     |
| 13  | [size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fwrite.html)<br>把 ptr 所指向的数组中的数据写入到给定流 stream 中。                              |
| 14  | [int remove(const char *filename)](https://www.runoob.com/cprogramming/c-function-remove.html)<br>删除给定的文件名 filename，以便它不再被访问。                                                                          |
| 15  | [int rename(const char *old_filename, const char *new_filename)](https://www.runoob.com/cprogramming/c-function-rename.html)<br>把 old_filename 所指向的文件名改为 new_filename。                                 |
| 16  | [void rewind(FILE *stream)](https://www.runoob.com/cprogramming/c-function-rewind.html)<br>设置文件位置为给定流 stream 的文件的开头。                                                                                   |
| 17  | [void setbuf(FILE *stream, char *buffer)](https://www.runoob.com/cprogramming/c-function-setbuf.html)<br>定义流 stream 应如何缓冲。                                                                             |
| 18  | [int setvbuf(FILE *stream, char *buffer, int mode, size_t size)](https://www.runoob.com/cprogramming/c-function-setvbuf.html)<br>另一个定义流 stream 应如何缓冲的函数。                                               |
| 19  | [FILE *tmpfile(void)](https://www.runoob.com/cprogramming/c-function-tmpfile.html)<br>以二进制更新模式(wb+)创建临时文件。                                                                                             |
| 20  | [char *tmpnam(char *str)](https://www.runoob.com/cprogramming/c-function-tmpnam.html)<br>生成并返回一个有效的临时文件名，该文件名之前是不存在的。                                                                                  |
| 21  | [int fprintf(FILE *stream, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-fprintf.html)<br>发送格式化输出到流 stream 中。                                                               |
| 22  | [int printf(const char *format, ...)](https://www.runoob.com/cprogramming/c-function-printf.html)<br>发送格式化输出到标准输出 stdout。                                                                              |
| 23  | [int sprintf(char *str, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-sprintf.html)<br>发送格式化输出到字符串。                                                                         |
| 24  | [int vfprintf(FILE *stream, const char *format, va_list arg)](https://www.runoob.com/cprogramming/c-function-vfprintf.html)<br>使用参数列表发送格式化输出到流 stream 中。                                               |
| 25  | [int vprintf(const char *format, va_list arg)](https://www.runoob.com/cprogramming/c-function-vprintf.html)<br>使用参数列表发送格式化输出到标准输出 stdout。                                                              |
| 26  | [int vsprintf(char *str, const char *format, va_list arg)](https://www.runoob.com/cprogramming/c-function-vsprintf.html)<br>使用参数列表发送格式化输出到字符串。                                                         |
| 27  | [int fscanf(FILE *stream, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-fscanf.html)<br>从流 stream 读取格式化输入。                                                                  |
| 28  | [int scanf(const char *format, ...)](https://www.runoob.com/cprogramming/c-function-scanf.html)<br>从标准输入 stdin 读取格式化输入。                                                                                |
| 29  | [int sscanf(const char *str, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-sscanf.html)<br>从字符串读取格式化输入。                                                                     |
| 30  | [int fgetc(FILE *stream)](https://www.runoob.com/cprogramming/c-function-fgetc.html)<br>从指定的流 stream 获取下一个字符（一个无符号字符），并把位置标识符往前移动。                                                                     |
| 31  | [char *fgets(char *str, int n, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fgets.html)<br>从指定的流 stream 读取一行，并把它存储在 str 所指向的字符串内。当读取 **(n-1)** 个字符时，或者读取到换行符时，或者到达文件末尾时，它会停止，具体视情况而定。 |
| 32  | [int fputc(int char, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fputc.html)<br>把参数 char 指定的字符（一个无符号字符）写入到指定的流 stream 中，并把位置标识符往前移动。                                                 |
| 33  | [int fputs(const char *str, FILE *stream)](https://www.runoob.com/cprogramming/c-function-fputs.html)<br>把字符串写入到指定的流 stream 中，但不包括空字符。                                                                 |
| 34  | [int getc(FILE *stream)](https://www.runoob.com/cprogramming/c-function-getc.html)<br>从指定的流 stream 获取下一个字符（一个无符号字符），并把位置标识符往前移动。                                                                       |
| 35  | [int getchar(void)](https://www.runoob.com/cprogramming/c-function-getchar.html)<br>从标准输入 stdin 获取一个字符（一个无符号字符）。                                                                                       |
| 36  | [char *gets(char *str)](https://www.runoob.com/cprogramming/c-function-gets.html)<br>从标准输入 stdin 读取一行，并把它存储在 str 所指向的字符串中。当读取到换行符时，或者到达文件末尾时，它会停止，具体视情况而定。                                             |
| 37  | [int putc(int char, FILE *stream)](https://www.runoob.com/cprogramming/c-function-putc.html)<br>把参数 char 指定的字符（一个无符号字符）写入到指定的流 stream 中，并把位置标识符往前移动。                                                   |
| 38  | [int putchar(int char)](https://www.runoob.com/cprogramming/c-function-putchar.html)<br>把参数 char 指定的字符（一个无符号字符）写入到标准输出 stdout 中。                                                                       |
| 39  | [int puts(const char *str)](https://www.runoob.com/cprogramming/c-function-puts.html)<br>把一个字符串写入到标准输出 stdout，直到空字符，但不包括空字符。换行符会被追加到输出中。                                                               |
| 40  | [int ungetc(int char, FILE *stream)](https://www.runoob.com/cprogramming/c-function-ungetc.html)<br>把字符 char（一个无符号字符）推入到指定的流 stream 中，以便它是下一个被读取到的字符。                                                  |
| 41  | [void perror(const char *str)](https://www.runoob.com/cprogramming/c-function-perror.html)<br>把一个描述性错误消息输出到标准错误 stderr。首先输出字符串 str，后跟一个冒号，然后是一个空格。                                                     |
| 42  | [int snprintf(char *str, size_t size, const char *format, ...)](https://www.runoob.com/cprogramming/c-function-snprintf.html)<br>格式字符串到 str 中。                                                         |

# <stdlib.h>

## 简介

- 定义了四个变量类型、一些宏和各种通用工具函数。

## 库变量

| **size_t**  | 无符号整数类型，它是 **sizeof** 关键字的结果 |
| ----------- | ---------------------------- |
| **wchar_t** | 宽字符常量大小的整数类型                 |
| **div_t**   | div** 函数返回的结构                |
| **ldiv_t**  | **ldiv** 函数返回的结构             |

## 库函数

| 序号  | 函数 & 描述                                                                                                                                                                                           |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [double atof(const char *str)](https://www.runoob.com/cprogramming/c-function-atof.html)<br>把参数 *str* 所指向的字符串转换为一个浮点数（类型为 double 型）。                                                              |
| 2   | [int atoi(const char *str)](https://www.runoob.com/cprogramming/c-function-atoi.html)<br>把参数 *str* 所指向的字符串转换为一个整数（类型为 int 型）。                                                                     |
| 3   | [long int atol(const char *str)](https://www.runoob.com/cprogramming/c-function-atol.html)<br>把参数 *str* 所指向的字符串转换为一个长整数（类型为 long int 型）。                                                          |
| 4   | [double strtod(const char *str, char **endptr)](https://www.runoob.com/cprogramming/c-function-strtod.html)<br>把参数 *str* 所指向的字符串转换为一个浮点数（类型为 double 型）。                                           |
| 5   | [long int strtol(const char *str, char **endptr, int base)](https://www.runoob.com/cprogramming/c-function-strtol.html)<br>把参数 *str* 所指向的字符串转换为一个长整数（类型为 long int 型）。                             |
| 6   | [unsigned long int strtoul(const char *str, char **endptr, int base)](https://www.runoob.com/cprogramming/c-function-strtoul.html)<br>把参数 *str* 所指向的字符串转换为一个无符号长整数（类型为 unsigned long int 型）。      |
| 7   | [void *calloc(size_t nitems, size_t size)](https://www.runoob.com/cprogramming/c-function-calloc.html)<br>分配所需的内存空间，并返回一个指向它的指针。                                                                  |
| 8   | [void free(void *ptr)](https://www.runoob.com/cprogramming/c-function-free.html)<br>释放之前调用 *calloc、malloc* 或 *realloc* 所分配的内存空间。                                                                  |
| 9   | [void *malloc(size_t size)](https://www.runoob.com/cprogramming/c-function-malloc.html)<br>分配所需的内存空间，并返回一个指向它的指针。                                                                                 |
| 10  | [void *realloc(void *ptr, size_t size)](https://www.runoob.com/cprogramming/c-function-realloc.html)<br>尝试重新调整之前调用 *malloc* 或 *calloc* 所分配的 ptr 所指向的内存块的大小。                                       |
| 11  | [void abort(void)](https://www.runoob.com/cprogramming/c-function-abort.html)<br>使一个异常程序终止。                                                                                                       |
| 12  | [int atexit(void (*func)(void))](https://www.runoob.com/cprogramming/c-function-atexit.html)<br>当程序正常终止时，调用指定的函数 **func**。                                                                        |
| 13  | [void exit(int status)](https://www.runoob.com/cprogramming/c-function-exit.html)<br>使程序正常终止。                                                                                                     |
| 14  | [char *getenv(const char *name)](https://www.runoob.com/cprogramming/c-function-getenv.html)<br>搜索 name 所指向的环境字符串，并返回相关的值给字符串。                                                                    |
| 15  | [int system(const char *string)](https://www.runoob.com/cprogramming/c-function-system.html)<br>由 string 指定的命令传给要被命令处理器执行的主机环境。                                                                   |
| 16  | [void *bsearch(const void *key, const void *base, size_t nitems, size_t size, int (*compar)(const void *, const void *))](https://www.runoob.com/cprogramming/c-function-bsearch.html)<br>执行二分查找。 |
| 17  | [void qsort(void *base, size_t nitems, size_t size, int (*compar)(const void *, const void*))](https://www.runoob.com/cprogramming/c-function-qsort.html)<br>数组排序。                                |
| 18  | [int abs(int x)](https://www.runoob.com/cprogramming/c-function-abs.html)<br>返回 x 的绝对值。                                                                                                           |
| 19  | [div_t div(int numer, int denom)](https://www.runoob.com/cprogramming/c-function-div.html)<br>分子除以分母。                                                                                             |
| 20  | [long int labs(long int x)](https://www.runoob.com/cprogramming/c-function-labs.html)<br>返回 x 的绝对值。                                                                                               |
| 21  | [ldiv_t ldiv(long int numer, long int denom)](https://www.runoob.com/cprogramming/c-function-ldiv.html)<br>分子除以分母。                                                                                |
| 22  | [int rand(void)](https://www.runoob.com/cprogramming/c-function-rand.html)<br>返回一个范围在 0 到 *RAND_MAX* 之间的伪随机数。                                                                                     |
| 23  | [void srand(unsigned int seed)](https://www.runoob.com/cprogramming/c-function-srand.html)<br>该函数播种由函数 **rand** 使用的随机数发生器。                                                                        |
| 24  | [int mblen(const char *str, size_t n)](https://www.runoob.com/cprogramming/c-function-mblen.html)<br>返回参数 *str* 所指向的多字节字符的长度。                                                                     |
| 25  | [size_t mbstowcs(schar_t *pwcs, const char *str, size_t n)](https://www.runoob.com/cprogramming/c-function-mbstowcs.html)<br>把参数 *str* 所指向的多字节字符的字符串转换为参数 *pwcs* 所指向的数组。                          |
| 26  | [int mbtowc(whcar_t *pwc, const char *str, size_t n)](https://www.runoob.com/cprogramming/c-function-mbtowc.html)<br>检查参数 *str* 所指向的多字节字符。                                                        |
| 27  | [size_t wcstombs(char *str, const wchar_t *pwcs, size_t n)](https://www.runoob.com/cprogramming/c-function-wcstombs.html)<br>把数组 *pwcs* 中存储的编码转换为多字节字符，并把它们存储在字符串 *str* 中。                        |
| 28  | [int wctomb(char *str, wchar_t wchar)](https://www.runoob.com/cprogramming/c-function-wctomb.html)<br>检查对应于参数 *wchar* 所给出的多字节字符的编码。                                                               |

# <string.h>

## 简介

- 定义了一个变量类型、一个宏和各种操作字符数组的函数。

## 库变量

| 序号         | 变量 & 描述                      |
| ---------- | ---------------------------- |
| **size_t** | 无符号整数类型，它是 **sizeof** 关键字的结果 |

## 库宏

| 序号       | 宏 & 描述         |
| -------- | -------------- |
| **NULL** | 这个宏是一个空指针常量的值。 |

## 库函数

| 序号 | 函数 & 描述                                                  |
| ---- | ------------------------------------------------------------ |
| 1    | [void *memchr(const void *str, int c, size_t n)](https://www.runoob.com/cprogramming/c-function-memchr.html)<br>在参数 *str* 所指向的字符串的前 n 个字节中搜索第一次出现字符 c（一个无符号字符）的位置。 |
| 2    | [int memcmp(const void *str1, const void *str2, size_t n)](https://www.runoob.com/cprogramming/c-function-memcmp.html)<br>把 *str1* 和 *str2* 的前 n 个字节进行比较。 |
| 3    | [void *memcpy(void *dest, const void *src, size_t n)](https://www.runoob.com/cprogramming/c-function-memcpy.html)<br>从 src 复制 n 个字符到 *dest*。 |
| 4    | [void *memmove(void *dest, const void *src, size_t n)](https://www.runoob.com/cprogramming/c-function-memmove.html)<br>另一个用于从 *src* 复制 n 个字符到 *dest* 的函数。<br />在重叠内存块方面比 memcpy 更安全，同步更改； |
| 5    | [void *memset(void *str, int c, size_t n)](https://www.runoob.com/cprogramming/c-function-memset.html)<br>复制字符 c（一个无符号字符）到参数 *str* 所指向的字符串的前 n 个字符。 |
| 6    | [char *strcat(char *dest, const char *src)](https://www.runoob.com/cprogramming/c-function-strcat.html)<br>把 *src* 所指向的字符串追加到 *dest* 所指向的字符串的结尾。 |
| 7    | [char *strncat(char *dest, const char *src, size_t n)](https://www.runoob.com/cprogramming/c-function-strncat.html)<br>把 *src* 所指向的字符串追加到 *dest* 所指向的字符串的结尾，直到 n 字符长度为止。 |
| 8    | [char *strchr(const char *str, int c)](https://www.runoob.com/cprogramming/c-function-strchr.html)<br>在参数 *str* 所指向的字符串中搜索第一次出现字符 c（一个无符号字符）的位置。 |
| 9    | [int strcmp(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strcmp.html)<br>把 *str1* 所指向的字符串和 *str2* 所指向的字符串进行比较。 |
| 10   | [int strncmp(const char *str1, const char *str2, size_t n)](https://www.runoob.com/cprogramming/c-function-strncmp.html)<br>把 *str1* 和 *str2* 进行比较，最多比较前 n 个字节。 |
| 11   | [int strcoll(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strcoll.html)<br>把 *str1* 和 *str2* 进行比较，结果取决于 LC_COLLATE 的位置设置。 |
| 12   | [char *strcpy(char *dest, const char *src)](https://www.runoob.com/cprogramming/c-function-strcpy.html)<br>把 *src* 所指向的字符串复制到 *dest*。 |
| 13   | [char *strncpy(char *dest, const char *src, size_t n)](https://www.runoob.com/cprogramming/c-function-strncpy.html)<br>把 *src* 所指向的字符串复制到 *dest*，最多复制 n 个字符。 |
| 14   | [size_t strcspn(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strcspn.html)<br>检索字符串 str1 开头连续有几个字符都不含字符串 str2 中的字符。 |
| 15   | [char *strerror(int errnum)](https://www.runoob.com/cprogramming/c-function-strerror.html)<br>从内部数组中搜索错误号 errnum，并返回一个指向错误消息字符串的指针。 |
| 16   | [size_t strlen(const char *str)](https://www.runoob.com/cprogramming/c-function-strlen.html)<br>计算字符串 str 的长度，直到空结束字符，但不包括空结束字符。 |
| 17   | [char *strpbrk(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strpbrk.html)<br>检索字符串 *str1* 中第一个匹配字符串 *str2* 中字符的字符，不包含空结束字符。也就是说，依次检验字符串 str1 中的字符，当被检验字符在字符串 str2 中也包含时，则停止检验，并返回该字符位置。 |
| 18   | [char *strrchr(const char *str, int c)](https://www.runoob.com/cprogramming/c-function-strrchr.html)<br>在参数 *str* 所指向的字符串中搜索最后一次出现字符 c（一个无符号字符）的位置。 |
| 19   | [size_t strspn(const char *str1, const char *str2)](https://www.runoob.com/cprogramming/c-function-strspn.html)<br>检索字符串 *str1* 中第一个不在字符串 *str2* 中出现的字符下标。 |
| 20   | [char *strstr(const char *haystack, const char *needle)](https://www.runoob.com/cprogramming/c-function-strstr.html)<br>在字符串 *haystack* 中查找第一次出现字符串 *needle*（不包含空结束字符）的位置。 |
| 21   | [char *strtok(char *str, const char *delim)](https://www.runoob.com/cprogramming/c-function-strtok.html)<br>分解字符串 *str* 为一组字符串，*delim* 为分隔符。 |
| 22   | [size_t strxfrm(char *dest, const char *src, size_t n)](https://www.runoob.com/cprogramming/c-function-strxfrm.html)<br>根据程序当前的区域选项中的 LC_COLLATE 来转换字符串 **src** 的前 **n** 个字符，并把它们放置在字符串 **dest** 中。 |

# <time.h>

## 简介

- 0定义了四个变量类型、两个宏和各种操作日期和时间的函数。

## 库变量

| 序号            | 变量 & 描述                        |
| ------------- | ------------------------------ |
| **size_t**    | 是无符号整数类型，它是 **sizeof** 关键字的结果。 |
| **clock_t**   | 这是一个适合存储处理器时间的类型。              |
| **time_t is** | 这是一个适合存储日历时间类型。                |
| **struct tm** | 这是一个用来保存时间和日期的结构。              |

m 结构的定义如下：

```cpp
struct tm {
   int tm_sec;         /* 秒，范围从 0 到 59        */
   int tm_min;         /* 分，范围从 0 到 59        */
   int tm_hour;        /* 小时，范围从 0 到 23        */
   int tm_mday;        /* 一月中的第几天，范围从 1 到 31    */
   int tm_mon;         /* 月，范围从 0 到 11        */
   int tm_year;        /* 自 1900 年起的年数        */
   int tm_wday;        /* 一周中的第几天，范围从 0 到 6    */
   int tm_yday;        /* 一年中的第几天，范围从 0 到 365    */
   int tm_isdst;       /* 夏令时                */
};
```

## 库宏

| 序号                 | 宏 & 描述           |
| ------------------ | ---------------- |
| **NULL**           | 这个宏是一个空指针常量的值。   |
| **CLOCKS_PER_SEC** | 这个宏表示每秒的处理器时钟个数。 |

## 库函数

| 序号  | 函数 & 描述                                                                                                                                                                                                           |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [char *asctime(const struct tm *timeptr)](https://www.runoob.com/cprogramming/c-function-asctime.html)<br>返回一个指向字符串的指针，它代表了结构 timeptr 的日期和时间。                                                                     |
| 2   | [clock_t clock(void)](https://www.runoob.com/cprogramming/c-function-clock.html)<br>返回程序执行起（一般为程序的开头），处理器时钟所使用的时间。                                                                                                |
| 3   | [char *ctime(const time_t *timer)](https://www.runoob.com/cprogramming/c-function-ctime.html)<br>返回一个表示当地时间的字符串，当地时间是基于参数 timer。                                                                                  |
| 4   | [double difftime(time_t time1, time_t time2)](https://www.runoob.com/cprogramming/c-function-difftime.html)<br>返回 time1 和 time2 之间相差的秒数 (time1-time2)。                                                            |
| 5   | [struct tm *gmtime(const time_t *timer)](https://www.runoob.com/cprogramming/c-function-gmtime.html)<br>timer 的值被分解为 tm 结构，并用协调世界时（UTC）也被称为格林尼治标准时间（GMT）表示。                                                       |
| 6   | [struct tm *localtime(const time_t *timer)](https://www.runoob.com/cprogramming/c-function-localtime.html)<br>timer 的值被分解为 tm 结构，并用本地时区表示。                                                                        |
| 7   | [time_t mktime(struct tm *timeptr)](https://www.runoob.com/cprogramming/c-function-mktime.html)<br>把 timeptr 所指向的结构转换为一个依据本地时区的 time_t 值。                                                                         |
| 8   | [size_t strftime(char *str, size_t maxsize, const char *format, const struct tm *timeptr)](https://www.runoob.com/cprogramming/c-function-strftime.html)<br>根据 format 中定义的格式化规则，格式化结构 timeptr 表示的时间，并把它存储在 str 中。 |
| 9   | [time_t time(time_t *timer)](https://www.runoob.com/cprogramming/c-function-time.html)<br>计算当前日历时间，并把它编码成 time_t 格式。                                                                                              |
