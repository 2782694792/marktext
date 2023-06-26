# C++ 标准库教程 —— 流

# <fstream>

## 1. 定义

    std::fstream

```cpp
template<
    class CharT,
    class Traits = std::char_traits<CharT>
>
class basic_fstream : public std::basic_iostream<CharT, Traits>
```

## 2. 参数

- `charT` —— 作为成员 `char_type` 的别名；

- `traits` —— 作为成员`traits_type` 的别名。

## 3. 成员类型

    用作参数或由成员函数返回类型：

| 成员类型        | 定义               |
| ----------- | ---------------- |
| char_traits | traits_type      |
| int         | const value_type |
| int         | int_type         |
| streampos   | pos_type         |
| streamoff   | off_type         |

## 4. 成员函数

| 函数                 | 描述                               |
| ------------------ | -------------------------------- |
| fstream::close     | 关闭当前与对象关联的文件，将其与流分离              |
| fstream::is_open   | 返回当前流是否与文件相关联                    |
| fstream::open      | 打开由参数 `filename` 标识的文件，将其与流对象相关联 |
| fstream::operator= | 返回一个常量迭代器，它指向数组的开始               |
| fstream::rdbuf     | 返回指向内部 `filebuf` 对象的指针           |
| fstream::swap      | 在 `x` 和 `*this` 之间交换所有内部数据       |
| 非成员重载函数            |                                  |
| swap-free          | 交换 `fstream` 对象 x 和 y 的值         |

### 4.1 close 分离

- 关闭与当前对象关联的文件，将其与流分离。

```cpp
void close();
```

> 并发访问同一个流可能会引入数据竞争。

### 4.2 isopen 关联

- 返回流当前是否与文件相关联。

- 流文件关联由其内部流缓冲区保存：在内部，函数调用 `rdbuf()->is_open()`。

```cpp
// C++98
bool is_open();
// C++11
bool is_open() const;
```

> 如果文件是打开的并且与当前流对象关联则函数返回 `true`，否则返回 `false`。

### 4.3 open 打开

- 打开文件是由参数 - `filename` 指定标识文件，将其与流对象相关联，以便对文件内容执行输入/输出操作。

```cpp
// C++98
void open (const char* filename, 
    ios_base::openmode mode = ios_base::in | ios_base::out);

// C++11
void open (const char* filename, 
    ios_base::openmode mode = ios_base::in | ios_base::out);
void open (const string& filename, 
    ios_base::openmode mode = ios_base::in | ios_base::out);
```

> ios_base::app 每次进入写操作时会重新定位到文件末尾，若不存在将创建对应文件。

### 4.4 operator=

    支持 C++11 标准版本的功能。通过移动和分配其成员和基类类获取右侧的内容。

```cpp
copy fstream& operator= (const fstream&) = delete;
move fstream& operator= (fstream&& rhs);
```

> 返回 `*this` 。

### 4.5 rdbuf 指向指针

    返回一个指向内部 `filebuf` 对象的指针。

```cpp
filebuf* rdbuf() const;
```

> std::fstream src;
> 
> std::filebuf * inbuf = src.rdbuf();

### 4.6 swap 交换

    交换`fstream`对象`x`和`y`的值。

```cpp
template <class charT, class traits>
  void swap (basic_fstream<charT, traits>& x, basic_fstream<charT, traits>& y);
```

    一个用于操作 C++ 程序输出的库，使用 C++，头文件提供参数化操纵器：

## 参数操纵器

| 成员类型          | 定义                |
| ------------- | ----------------- |
| setiosflags   | 用于设置格式标志          |
| resetiosflags | 用于重置格式标志          |
| setbase       | 用于设置 basefield 标志 |
| setfill       | 设置填充字符            |
| setprecision  | 设置小数精度            |
| setw          | 设置字段宽度            |
| get_money     | 获得货币值             |
| put_money     | 设置计算货币的值          |
| get_time      | 获取日期和时间           |
| put_time      | 放置（设置）日期和时间       |

### 1.1 setiosflags 掩码表示

- `std::resetiosflags` 的行为类似成员 `setf` 使用 `mask` 作为参数在作为操纵器插入/提取（输入、输出流上插入/提取）的流上被调用。

```cpp
setiosflags(ios_base::fmtflags nark);

std::cout << std::hex;
std::cout << std::setiosflags (std::ios::showbase | std::ios::uppercase);
std::cout << 100 << std::endl;
-- 0X64
```

> `mask` —— 掩码表示要设置的标志。
> 
> `fmtflags` —— 位掩码（`bitmask`）类型。

### 1.2 resetiosflags 取消掩码表示

- `std::resetiosflags` 的行为类似成员 `unsetf` 使用 mask 作为参数在作为操纵器插入/提取（输入、输出流上）的流上被调用。

```cpp
resetiosflags(ios_base::fmtflags nark);

std::cout << std::hex << std::setiosflags (std::ios::showbase);
std::cout << 100 << std::endl; 
-- 0x64
std::cout << std::resetiosflags(std::ios::showbase) << 100 << std::endl;
-- 64
```

### 1.3 setbase 设置基数

- （ios_base::basefield）在被插入/提取为操纵器的流上被调用，其中，
  
  - dec，基数是 10；
  
  - hex，基数是 16；
  
  - oct，基数是 8；
  
  - 0，基数是任何其他值。

```cpp
setbase(int base);
```

> 返回 unspecified ，此函数仅应用于流操纵器。

### 1.4 setfill 填充

- 在流上调用 `c` 作为参数的成员填充，以操纵器插入（输出流）。

```cpp
setfill (char_type c);

std::cout << std::setfill ('y') << std::setw (10);
std::cout << 77 << std::endl;
-- yyyyyyyy77
```

> `char-type` —— 流使用的字符类型（第一个类模板参数 charT）；
> 
> 返回 `unspecified` ，仅应用于流操纵器；

### 1.5 setprecision 小数精度

- 在流上调用 n 作为参数的成员精度，以操纵器插入 / 提取（输入输出流）；

- 用于设置在输出操作上格式化浮点值的小数精度。

```cpp
setprecision (int n);

f = 3.14159
std::cout << std::setprecision(5) << f << '/n';
-- 3.1416
```

> `n` —— 新的小数精度值；
> 
> 返回 `unspecified` ，函数仅应用于流操纵器。

### 1.6 setw 字段宽度

- 用于设置要在输出操作上使用的字段宽度。

```cpp
setw(int n);
```

> `n` − 用作字段宽度的字符数；
> 
> 返回未指定（`unspecified`），函数仅用于流操纵器。

### 1.7 get_money 货币表达

- 首先，构造一个类型为 `basic_istream::sentry` 的对象（ `noskipws` 设置为 `false` ）来访问输入序列；

- 然后（如果 `sentry` 对象是 `true` ），它调用 `money_get::get`（使用流的所选地区）来执行提取和解析操作，并相应地调整流的内部状态标志；

- 最后，它在返回之前销毁 `sentry` 对象。

- 用于从应用程序输入流的字符中提取字符，并解释为货币表达式，存储为 `mon` 的值。

```cpp
template <class moneyT>
get_money (const moneyT& mon, bool intl = false);

std::cin >> std::get_money(moneyT);
if (std::cin.fail())
    ...
```

> `mon` —— 货币值。`moneyT`应该是`long double`或者`basic_string`一个实例；
> 
> `intl` —— `true`表示国际化，否则`false`。内部用于实例化适当的多用途类。
> 
> 返回未指定（`unspecified`），函数仅用作流操纵器。

### 1.8 put_money 货币表达输出

- 首先，通过构造一个类型为 `basic_ostream::sentr` 的对象来访问输出序列；

- 然后（如果计算 `sentry` 对象是 `true`），它调用 `money_put::put`（使用流的选择的区域设置）来执行格式化和插入操作，相应地调整流的内部状态标志；

- 最后，它在返回之前销毁 `sentry` 对象。

- 用于将货币的表示作为货币值插入到其应用的输出流中。

```cpp
template <class moneyT>
put_money (const moneyT& mon, bool intl = false);

std::cout << "Price:" << std::put_money(10.50L) << '/n';
-- Price:10
```

### 1.9 get_time 时间解析

- 首先，通过构造一个类型为 `basic_istream::sentry` 的对象来访问输入序列；

- 然后（如果计算 `sentry` 对象为 `true`），它调用 `time_get::get`（使用流的所选区域设置）来执行提取和解析操作，并相应地调整流的内部状态标志；

- 最后，它在返回之前销毁 `sentry` 对象。

- 用于从应用中输入流的字符中提取字符，并将它们解析为参数 `fmt` 中指定的时间和日期信息。获得的数据存储在 `tmb` 指向的 `struct tm` 对象。

```cpp
template <class charT>
/*unspecified*/ get_time (struct tm* tmb, const charT* fmt);

struct std::tm when;
std::cout << "Please, enter the time: ";
std::cin >> std::get_time(&when,"%R");   
if (std::cin.fail()) 
    std::cout << "Error reading time/n";
else {
    std::cout << "The time entered is: ";
    std::cout << when.tm_hour << " hours and " << when.tm_min << " minutes/n";
  }
```

> - `tmb` —— 指向`struct tm`类型的对象的指针，其中存储提取的时间和日期信息。
> 
> - `fmt` —— `time_get::get`使用 C字符串作为格式字符串（见 `time_get::get`）。

### 1.10 put_time 时间格式化

- 首先通过构造一个类型为 `basic_ostream::sentry` 的对象来访问输出序列；

- 然后（如果评估计算 `sentry` 对象为 `true`），它调用 `time_put::put`（使用流的选定区域设置）来执行格式化和插入操作，相应地调整流的内部状态标志；

- 最后，它在返回之前销毁 `sentry` 对象。

- 用于插入由 `tmb` 指向的时间和日期信息的表示，根据参数 `fmt` 指定来格式化。

```cpp
template <class charT>
/*unspecified*/ put_time (const struct tm* tmb, const charT* fmt);
```

> - `tmb` − 指向类型`struct tm`的对象的指针，具有格式化的日期和时间信息；
> 
> - `fmt` − `time_put::put` 使用C字符串作为格式字符串。它包含常规字符和特殊格式说明符的任意组合。

- 格式都以百分比（`％`）符号开头：

| 说明符                                                                    | 替换         | 示例  |
| ---------------------------------------------------------------------- | ---------- | --- |
| %a                                                                     | 工作日名称的缩写 * | Thu |
| [其他说明符](https://www.yiibai.com/cpp_standard_library/cpp_put_time.html) |            |     |

```cpp
#include <iostream>
#include <iomanip>
#include <ctime>
#include <chrono>

int main () {
   using std::chrono::system_clock;
   std::time_t tt = system_clock::to_time_t (system_clock::now());

   struct std::tm * ptm = std::localtime(&tt);
   std::cout << "Now (local time): " << std::put_time(ptm,"%c") << '/n';

   return 0;
}
```

# <basic_ios>

- [basic_ios::basic_ios 构造函数](https://www.yiibai.com/cpp_standard_library/cpp_ios_constructor.html)；

- [basic_ios::basic_ios 析构函数](https://www.yiibai.com/cpp_standard_library/cpp_ios_destructor.html)。

## 1. 定义

```cpp
template <class charT, class traits = char_traits<charT> >
class ios_base;
```

> `traits` : 字符 traits 类定义了流对象使用的字符的基本属性。

## 2. 成员类型

| 成员类型           | 定义         |
| -------------- | ---------- |
| event          | 类型，表示事件类型  |
| event_callback | 事件回调函数类型   |
| failure        |            |
| fmtflags       | 流格式标志的类型   |
| Init           | 初始化标准流对象   |
| iostate        | 用于流状态标志的类型 |
| openmode       | 流打开模式标志的类型 |
| seekdir        | 流查找方向标志的类型 |

### 2.1 event 事件枚举

- 用作为`ios_base::register_callback`注册函数的第一个参数。

- 标识触发函数调用的事件类型。

```cpp
enum event { erase_event, imbue_event, copyfmt_event };

-- erase_event : 调用流析构函数（ios::copyfmt 的开头调用）
-- imbue_event : 调用 ios_base::imbue（函数返回之前）
-- copyfmt_event : 调用 ios::copyfmt（在所有格式标志都已复制，但在异常掩码之前）
```

### 2.2 event_callback 回调事件

- 用于注册成员 `register_callback` 的回调函数

```cpp
typedef void (*event_callback) (event ev, ios_base& obj, int index);

-- ev : 枚举成员类型事件的对象
-- obj : 调用回调函数时，对事件触发的流对象的引用（*this）
-- index : 调用回调函数时，函数注册成员 register_callback 时，
           它被设置为与索引参数相同的值
```

### 2.4 fmtflags

- 被成员函数 `flags`，`setq` 和 `setq` 用作其参数和/或返回值。

```cpp
std::ios_base::fmtflags ff;


   std::cout.setf (std::ios_base::hex , std::ios_base::basefield);
   std::cout.setf (std::ios_base::showbase);
   std::cout << 100 << '/n'; // 0x64

   std::cout.setf (std::ios::hex , std::ios::basefield);
   std::cout.setf (std::ios::showbase);
   std::cout << 100 << '/n'; // 0x64

   std::cout.setf (std::cout.hex , std::cout.basefield);
   std::cout.setf (std::cout.showbase);
   std::cout << 100 << '/n'; // 0x64

   std::ios_base::fmtflags ff;
   ff = std::cout.flags();
   ff &= ~std::cout.basefield;   
   ff |= std::cout.hex;          
   ff |= std::cout.showbase;     
   std::cout.flags(ff);
   std::cout << 100 << '/n'; // 0x64

   std::cout << std::hex << std::showbase << 100 << '/n'; // 0x64
```

> 常量的值可以使用 `OR` 按位运算符（`|`）组合成单个 `fmtflags` 值。

### 2.5 init 初始化

- 用于构造此成员类型的对象，确保标准流对象（cin，cout，cerr，clog，cin，cout，cerr和clog）被构造和正确初始化。

```cpp
class Init;

// 类维护一个内部静态计数器与现有对象的数量
class ios_base::Init {
   static int init_cnt;  
public:
   Init();
   ~Init();
}
```

### 2.6 iostate 流状态标志

- 用于所有流对象在内部保持关于对象的状态的信息；

- 通过调用成员函数 `basic_ios::red state` 或通过调用 `basic_ios::setstate` 设置，用来作为此类型的元素检索。

| 标志值     | 指示                          |
| ------- | --------------------------- |
| eofbit  | 在对输入流执行提取操作时到达文件结束          |
| failbit | 最后一个输入操作失败，因为操作本身的内部逻辑相关的错误 |
| badbit  | 流缓冲区上的输入/输出操作失败导致的错误        |
| goodbit | 没有错误。 表示不存在上述所有（值为零）        |

### 2.7 openmode 流开放模式

- `Bitmask` 类型用来表示流开放模式标志。

| 成员常数   | 打开模式                                 |
| ------ | ------------------------------------ |
| app    | （**append**）在每个输出操作之前将流的位置指示符设置为流的结尾 |
| ate    | （**at end**）将流的位置指示器设置为打开时流的结尾       |
| binary | 将流视为二进制                              |
| in     | 允许对流进行输入操作                           |
| out    | 允许对流进行输出操作                           |
| trunc  | （truncate）任何当前内容都被丢弃，假设打开时的长度为零      |

### 2.8 seekdir 流操作方向

- 这是枚举类型用来表示流查找操作的寻找方向。

| 成员函数 | 查找相对  |
| ---- | ----- |
| beg  | 序列的开始 |
| cur  | 当前位置  |
| end  | 序列的结尾 |

## 3. 状态标志函数

| 成员类型     | 定义                       |
| -------- | ------------------------ |
| good     | 用于检查流的状态是否良好             |
| eof      | 用于检查是否设置了eofbit          |
| fail     | 用于检查是否设置了failbit或badbit位 |
| bad      | 用于检查是否设置了 badbit         |
| 操作符 !    | 评估计算流                    |
| rdstate  | 用于获取错误状态标志               |
| setstate | 用于设置错误状态标志               |
| clear    | 用于清除状态标志                 |

### 3.1 流状态判断

- ios::good()、ios::eof()、ios::fail()、ios::bad() 用于检查流的状态；

```cpp
bool good() const;
-- 没有设置流的状态标志则返回 true ，设置了返回 false
bool eof() const;
-- 设置流的错误状态标志 eofbit 则返回 true
bool fail() const;

bool bad() const;
```

    示例：

```cpp
#include <iostream>     
#include <sstream>      

void print_state (const std::ios& stream) {
   std::cout << " good()=" << stream.good();
   std::cout << " eof()=" << stream.eof();
   std::cout << " fail()=" << stream.fail();
   std::cout << " bad()=" << stream.bad();
}

int main () {
   std::stringstream stream;

   stream.clear (stream.goodbit);
   std::cout << "goodbit:"; print_state(stream); std::cout << '/n';

   stream.clear (stream.eofbit);
   std::cout << " eofbit:"; print_state(stream); std::cout << '/n';

   stream.clear (stream.failbit);
   std::cout << "failbit:"; print_state(stream); std::cout << '/n';

   stream.clear (stream.badbit);
   std::cout << " badbit:"; print_state(stream); std::cout << '/n';

   return 0;
}
```

### 3.2 运算符 ！

- 用于检查评估流（非）。 这相当于调用成员失败。

```cpp
bool operator!() const;

-- 如果设置了 failbit 或 badbit 则返回 true
```

### 3.3 rdstate 获取错误标志

- 用于检查获取错误状态标志。 

- 内部错误状态标志通过调用流上的输入/输出函数来自动设置，以指示某些错误。

```cpp
iostate rdstate() const;
```

### 3.4 clear 清除错误标志

- 用于设置错误状态标志；

- 标志的当前值被覆盖：所有位被状态中的那些位替换; 

- 如果状态为 `goodbit`（为零），则所有错误标志被清除；

- 调用此函数时，如果没有流缓冲区与流相关联，则会自动设置`badbit`标志。

```cpp
void clear (iostate state = goodbit);
```

## 4. 格式化

| 成员类型    | 定义        |
| ------- | --------- |
| copyfmt | 复制格式信息    |
| fill    | 获取/设置填充字符 |

### 4.1 copyfmt 复制流

- 将 `rhs` 的所有内部成员的值（状态标志和关联的流缓冲区除外）复制到 `*this` 的相应成员。

```cpp
ios& copyfmt (const ios& rhs);

-- rhs : Stream对象，其成员将被复制到 *this
```

### 4.2 fill 填充

- 用于获取/设置填充字符。填充字符是用于填充空格的字符到字段宽度在输出插入函数时。

```cpp
char fill() const;       // 获取填充字符

char fill (char fillch); // 设置填充字符
```

## 5. 其它

| 成员类型       | 定义          |
| ---------- | ----------- |
| exceptions | 用于获取/设置异常掩码 |
| imbue      | Imbue 语言环境  |
| tie        | 用于获取/设置绑定流  |
| rdbuf      | 用于获取/设置流缓冲区 |
| narrow     | 缩窄字符        |
| widen      | 加宽字符        |

### 5.1 exceptions 异常掩码

- 用于获取/设置异常掩码；

- 异常掩码是所有流对象保存的内部值，指定为哪些状态标志设置时抛出成员类型失败（或某些派生类型）的异常。

```cpp
iostate exceptions() const;        // 获取异常掩码
void exceptions (iostate except);  // 设置异常掩码，并清除流的错误状态标志

file.exceptions ( std::ifstream::failbit | std::ifstream::badbit );
```

> 调用此成员函数之前返回表示现有异常掩码的成员类型 `iostate` 的位掩码。

### 5.2 imbue 设置区域对象

- 关联 `loc` 到流和流缓冲区（如果有），用于区域设置敏感操作的新区域设置对象；

- 函数调用它的继承同义词 `ios_base::imbue(loc)`，如果流与流缓冲区相关联，它也调用`rdbuf() -> pubimbue(loc)` 。

```cpp
locale imbue (const locale& loc);
```

### 5.3 tie 操作流

- 用于获取/设置绑定流；

- C++98 ：默认情况下，`cin`绑定到`cout`，`wcin`绑定到`wcout`。库实现可以在初始化时绑定其他标准流；

- C++11 ：默认情况下，标准流 `cin` 和 `cerr` 与 `cout` 绑定，它们的宽字符对应（`wcin`和`wcerr`）绑定到`wcout`。 库实现也可以绑定`clog`和`wclog` 。

```cpp
ostream* tie() const;           // 返回指向绑定输出流的指针
ostream* tie (ostream* tiestr); // 绑定到 tiestr，并返回指向调用之前绑定的流的指针


   std::ostream *prevstr;
   std::ofstream ofs;
   ofs.open ("test.txt");

   std::cout << "tie example:/n";

   *std::cin.tie() << "This is inserted into cout";
   prevstr = std::cin.tie (&ofs);
   *std::cin.tie() << "This is inserted into the file";
   std::cin.tie (prevstr);


-- tie example:
-- This is inserted into cout
```

### 5.4 rdbuf 操作缓冲区

- 用于获取/设置流缓冲区；

- 如果`sb`是一个空指针，函数会自动设置`badbit`错误状态标志（如果成员异常已经传递`badbit`，它可能会抛出异常）。

```cpp
streambuf* rdbuf() const;
streambuf* rdbuf (streambuf* sb);
```

### 5.5 narrow 缩小字符

- 用于缩小字符，并使用当前在流中的语言环境对象的`ctype :: narrow`  返回 `c` 的等效变换，否则返回`dfault`。

```cpp
char narrow (char c, char dfault) const;

-- c : 用于“缩小”的字符
-- dfault : 如果 c 没有标准等效值，则返回默认值
-- 强保证 - 如果抛出异常，流中没有任何更改。
```

### 5.6 widen 扩宽字符

- 用于查找扩宽字符，并使用当前在流中包含的语言环境对象的`ctype :: widen` 面，将c的变换返回到其等效变换。

```cpp
char widen (char c) const;
```

## 6. 受保护的成员函数

| 成员类型      | 定义       |
| --------- | -------- |
| init      | 初始化对象    |
| move      | 移动内部     |
| swap      | 交换内部     |
| set_rdbuf | 用于设置流缓冲区 |

### 6.1 init 初始化流

- 用于初始化流的内部标志和成员变量的值。

```cpp
protected: void init (streambuf* sb);
```

### 6.2 move 传递成员

- 将`x`的所有内部成员传递给`* this`，除了关联的流缓冲区（rdbuf 调用后返回空指针）。

```cpp
void move (ios&  x);
void move (ios&& x);
```

> `x` − 流对象的成员被移动到`* this`。

### 6.3 swap 交换成员

- 交换 `x` 和 `*this` 之间所有内部成员，除指向相关联的流缓冲区指针：`rdbuf` 在调用之前在两个对象中返回相同的值。

```cpp
void swap (ios& x) noexcept;
```

### 6.4 set_rdbuf 设置流缓冲区

- 将 `sb` 设置为与流相关联的流缓冲区，不改变控制状态标志（`rdstate`），且不为空指针。

```cpp
void set_rdbuf (streambuf* sb);
```

### 6.5 fpos 指示流位置

- 用于流位置类模板。类模板用作类型的模板，用于指示流中的位置。

```cpp
stateT state() const;
void state(stateT);

C++98
template <class stateT> class fpos;
```

# <ios>

用于流类型基类（依赖组件）。

## 定义

```cpp
typedef basic_ios<char> ios;
```

## 参数

- `charT` − 字符类型；

- `traits` − 成员`traits_type`的别名。

## 成员类型
