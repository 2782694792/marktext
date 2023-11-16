## 1    排版

### 1-1：缩进风格

- 程序块要采用缩进风格编写，缩进的空格数为 4 个；

### 1-2：相对独立的程序块之间分隔

- 相对独立的程序块之间、变量说明之后必须加空行；

```cpp
示例：

if (!valid_ni(ni))  
{  
 ... // program code  
}  

repssn_ind = ssn_data[index].repssn_index;  
repssn_ni = ssn_data[index].ni; 
```

### 1-3：语句（>80 字符）分多行书写

- 长表达式要在低优先级操作符处划分新行，操作符放在新行之首，划分出的新行要适当缩进；

```cpp
perm_count_msg.head.len = NO7_TO_STAT_PERM_COUNT_LEN 
                     + STAT_SIZE_PER_FRAM * sizeof( _UL ); 

act_task_table[frame_id * STAT_TASK_CHECK_NUMBER + index].occupied 
             = stat_poi[index].occupied;

report_or_not_flag = ((taskno < MAX_ACT_TASK_NUMBER)
                && (n7stat_stat_item_valid (stat_item)) 
                && (act_task_table[taskno].result_data != 0));
```

### 1-4：划分长表达式

- 循环、判断等语句中有较长的表达式或语句；
- 进行适应划分，长表达式要在低优先级操作符处划分新行，操作符放在新行之首；

```cpp
示例：
if ((taskno < max_act_task_number) 
    && (n7stat_stat_item_valid (stat_item))) 
{ 
    ... // program code 
} 
for (i = 0, j = 0; (i < BufferKeyword[word_index].word_length) 
                && (j < NewKeyword.word_length); i++, j++) 
{ 
    ... // program code 
} 

for (i = 0, j = 0; 
    (i < first_word_length) && (j < second_word_length); 
    i++, j++) 
{ 
    ... // program code 
}
```

### 1-5：关键字语句独占一行

- if、for、do、while、case、switch、default 等语句自占一行，且if、for、do、while等的执行语句部分要加括号 { }；

```cpp
if (pUserCR == NULL) 
{ 
     return; 
}
```

### 1-6：空格对齐

- 对齐只使用**空格键**，不使用TAB键；

```c++
//说明：以免用不同的编辑器阅读程序时，因 TAB 键所设置的空格数目不同而造成程序布局
//不整齐，不要使用 BC 作为编辑器合版本，因为 BC 会自动将 8 个空格变为一个 TAB 键，
//因此使用 BC 合入的版本大多会将缩进变乱。
```

### 1-7：确定操作符是否加空格

- 在两个以上关键字、变量、常量对等操作时，之间的操作符之前、之后或前后加空格；非对等操作时，**关系密切的立即操作符（如－>）后不加空格**；

```cpp
(1)"!"、"~"、"++"、"--"、"&"（地址运算符）等单目操作符前后不加空格。
*p = 'a'; // 内容操作"*"与内容之间
flag = !isEmpty; // 非操作"!"与内容之间
p = &mem; // 地址操作"&" 与内容之间
i++; // "++","--"与内容之间

(2)"->"、"."前后不加空格。
p->id = pid; // "->"指针前后不加空格

(3) if、for、while、switch 等与后面的括号间应加空格，使 if 等关键字更为突出、明显。
if (a >= b && c > d)
```

## 2    注释

### 2-1：说明性文件头部应注释

- 文件（.h文件、.inc文件、.def文件、编译说明文件 .cfg 等）头部应注释；
- 必须列出：版权说明、版本号、生成日期、作者、内容、功能、与其它文件的关系、修改日志等，还应有函数功能简要说明；

```cpp
/*************************************************** 
 Copyright (C), Your Company 
 File name: // 文件名
 Author: Version: Date: // 作者、版本及完成日期
 Description: // 用于详细说明此程序文件完成的主要功能，与其他模块
              // 或函数的接口，输出值、取值范围、含义及参数间的控
              // 制、顺序、独立或依赖等关系
 Others: // 其它内容的说明
 Function List: // 主要函数列表，每条记录应包括函数名及功能简要说明
   1. .... 
 History: // 修改历史记录列表，每条修改记录应包括修改日期、修改
          // 者及修改内容简述 
   1. Date: 
      Author: 
      Modification: 
   2. ... 
***************************************************/
```

### 2-2：源文件头部应注释

- 列出：版权说明、版本号、生成日期、作者、模块目的/功能、主要函数及其功能、修改日志等。

```cpp
/************************************************************ 
 Copyright (C), 1988-1999, Huawei Tech. Co., Ltd. 
 FileName: test.cpp 
 Author: Version : Date: 
 Description: // 模块描述 
 Version: // 版本信息
 Function List: // 主要函数及其功能
   1. ------- 
 History: // 历史修改记录
     <author> <time> <version > <desc> 
     David 96/10/12 1.0 build this moudle 
***********************************************************/ 
说明：Description 一项描述本文件的内容、功能、内部各部分之间的关系及本文件与
其它文件关系等。History 是修改历史记录列表，每条修改记录应包括修改日期、修改
者及修改内容简述
```

### 2-3：函数头部应注释

- 列出：函数的目的/功能、输入参数、输出参数、返回值、调用关系（函数、表）等；

```cpp
/************************************************* 
 Function: // 函数名称
 Description: // 函数功能、性能等的描述
 Calls: // 被本函数调用的函数清单
 Called By: // 调用本函数的函数清单
 Table Accessed: // 被访问的表（此项仅对于牵扯到数据库操作的程序）
 Table Updated: // 被修改的表（此项仅对于牵扯到数据库操作的程序）
 Input: // 输入参数说明，包括每个参数的作
        // 用、取值说明及参数间关系。
 Output: // 对输出参数的说明。
 Return: // 函数返回值的说明
 Others: // 其它说明
*************************************************/
```

### 2-4：数据结构声明

- 数据结构（数组、结构、类、枚举等）应进行声明，如果其命名不充分自注释则须加以注释。
- 对数据结构的注释应放其上方；
- 结构中每个域的注释放右方；

```cpp
示例：可按如下形式说明枚举/数据/联合结构。
/* sccp interface with sccp user primitive message name */ 
enum SCCP_USER_PRIMITIVE 
{
    N_UNITDATA_IND, /* sccp notify sccp user unit data come */ 
    N_NOTICE_IND, /* sccp notify user the No.7 network can not */ 
                 /* transmission this message */ 
    N_UNITDATA_REQ, /* sccp user's unit data transmission request*/ 
};
```

### 2-5：全局变量要有详细注释

- 包括对其功能、取值范围、哪些函数或过程存取它以及存取时注意事项等的说明；

```cpp
/* The ErrorCode when SCCP translate */ 
/* Global Title failure, as follows */ // 变量作用、含义
/* 0 － SUCCESS 1 － GT Table error */ 
/* 2 － GT error Others － no use */ // 变量取值范围
/* only function SCCPTranslate() in */ 
/* this modual can modify it, and other */ 
/* module can visit it through call */ 
/* the function GetGTTransErrorCode() */ // 使用方法
BYTE g_GTTranErrorCode;
```

### 2-6：注释与描述内容进行同样缩排

```cpp
void example_fun( void ) 
{ 
    /* code one comments */ 
     CodeBlock One 

    /* code two comments */ 
     CodeBlock Two 
}
```

### 2-7：注释与前面代码用空行隔开

```cpp
/* code one comments */ 
program code one 

/* code two comments */ 
program code two
```

### 2-8：程序块的结束行加注释标记

- 表明某程序块的结束；

```cpp
if (...) 
{ 
    // program code 
     while (index < MAX_INDEX) 
     { 
         // program code 
     } /* end of while (index < MAX_INDEX) */ // 指明该条 while 语句结束
} /* end of if (...)*/ // 指明是哪条 if 语句结束
```

### 2-9：注释统一，建议 /* …… */

## 3    标识符命名

### 3-1：单词缩写

- 较短单词通过去掉“元音”形成缩写；
- 较长单词取头几个字母形成缩写；
- 一些有公认缩写。

```C++
temp 可缩写为 tmp ; 
flag 可缩写为 flg ;  
statistic 可缩写为 stat ;  
increment 可缩写为 inc ;  
message 可缩写为 msg ;
```

### 3-2：注释说明特殊约定或缩写

### 3-3：单字符（i、j、k）说明

- 有具体含义外，还能表明变量类型、数据类型等，可作局部循环变量

```C++
int liv_Width;
/*
 其变量名解释如下：
 m 成员变量（member）
 l 局部变量（Local） （其它：g 全局变量（Global）...）
 i 数据类型（Interger）
 v 变量（Variable） （其它：c 常量（Const）...）
 Width 变量含义
 */
```

### 3-4：标识后加大小写混排

- 不用大小写与下划线混排；
- 用作特殊标识如标识成员或全局变量的 m_ 和 g_ ，后加大小写混排的方式；

```C++
示例： Add_User 不允许，add_user、AddUser、m_AddUser 允许。
```

### 3-5：反义词命名

- 命名组成具有互斥意义的变量或相反动作的函数等；

```C++
/*
  add / move    create / destory    insert / delete
  add / delete    first / last        increment / decrement
  put / get        get / release        old / new
  start / stop    begin / end            open / close
  min / max        lock / unlock        next / previous
  cut / paste    send / receive        show / hide
  up / down        source / target        source / destination
 */
```

## 4    可读性

### 4-1：用标识（枚举或宏）替代

- 涉及物理状态或含有物理意义的常量，用有意义的枚举或宏代替

```cpp
示例：如下的程序可读性差。
if (Trunk[index].trunk_state == 0) 
{ 
     Trunk[index].trunk_state = 1; 
    ... // program code 
} 

应改为如下形式。
#define TRUNK_IDLE 0 
#define TRUNK_BUSY 1 
if (Trunk[index].trunk_state == TRUNK_IDLE) 
{ 
    Trunk[index].trunk_state = TRUNK_BUSY; 
     ... // program code 
}
```

### 4-2：关系紧密的代码保持相邻

```cpp
rect.length = 10; 
rect.width = 5; // 矩形的长与宽关系较密切，放在一起。
char_poi = str;
```

## 5    变量、结构

### 5-1：防止局部变量与公共变量同名

### 5-2：去掉没必要的公共变量

> 说明：公共变量是增大模块间耦合的原因之一，故应减少没必要的公共变量以降低模块间的耦合度

### 5-3：结构功能要单一

- 是针对一种事务的抽象；

```cpp
示例：如下结构不太清晰、合理。
typedef struct STUDENT_STRU 
{
    unsigned char name[8]; /* student's name */ 
     unsigned char age; /* student's age */ 
     unsigned char sex; /* student's sex, as follows */ 
                     /* 0 - FEMALE; 1 - MALE */ 
     unsigned char 
         teacher_name[8]; /* the student teacher's name */ 
     unsigned char 
         teacher_sex; /* his teacher sex */ 
} STUDENT; 

若改为如下，可能更合理些:

typedef struct TEACHER_STRU 
{ 
 unsigned char name[8]; /* teacher name */ 
 unisgned char sex; /* teacher sex, as follows */ 
 /* 0 - FEMALE; 1 - MALE */ 
} TEACHER; 

typedef struct STUDENT_STRU 
{ 
    unsigned char name[8]; /* student's name */ 
     unsigned char age; /* student's age */ 
     unsigned char sex; /* student's sex, as follows */ 
                     /* 0 - FEMALE; 1 - MALE */ 
     unsigned int teacher_ind; /* his teacher index */ 
} STUDENT;
```

### 5-4：不设计特别灵活的数据结构

> 说明：面面俱到、灵活的数据结构反而容易引起误解和操作困难

### 5-5：设计结构中元素的布局与排列

- 使结构容易理解、节省占用空间，并减少引起误用现象

```cpp
示例：如下结构中的位域排列，将占较大空间，可读性也稍差。
typedef struct EXAMPLE_STRU 
{ 
     unsigned int valid: 1; 
     PERSON person; 
     unsigned int set_flg: 1; 
} EXAMPLE; 

若改成如下形式，不仅可节省 1 字节空间，可读性也变好了。
typedef struct EXAMPLE_STRU 
{
    unsigned int valid: 1; 
    unsigned int set_flg: 1; 
    PERSON person ; 
} EXAMPLE;
```

## 6    函数、过程

### 6-1：可重入函数注意局部变量

- 如使用auto即缺省态局部变量或寄存器变量；

> 说明：编写 C/C++语言的可重入函数时，不应使用 static 局部变量，否则必须经过特殊处理，才能使函数具有可重入性。

### 6-2：可重入函数注意保护全局变量

- 使用全局变量，则应通过关中断、信号量（即P、V操作）等手段加以保护；

> 说明：若对所使用的全局变量不加以保护，则此函数就不具有可重入性，即当多个进程调用此函数时,很有可能使有关全局变量变为不可知状态

```cpp
unsigned int example( int para ) 
{ 
    unsigned int temp; 
    Exam = para; // （**）
    temp = Square_Exam( ); 
    return temp; 
}
// 此函数若被多个进程调用的话，其结果可能是未知的，因为当（**）语句刚执行完后，另
// 外一个使用本函数的进程可能正好被激活，那么当新激活的进程执行到此函数时，将使
// Exam 赋与另一个不同的 para 值，所以当控制重新回到“temp = Square_Exam( )”
// 后，计算出的 temp 很可能不是预想中的结果。此函数应如下改进。

unsigned int example( int para ) 
{ 
    unsigned int temp; 

    // [申请信号量操作]
    Exam = para;
    temp = Square_Exam( ); 
    // [释放信号量操作]    

    return temp; 
}
```

### 6-3：防止将函数参数作为工作变量

> 说明：将函数的参数作为工作变量，有可能错误地改变参数内容，所以很危险。对必须改变的参数，最好先用局部变量代之，最后再将该局部变量的内容赋给该参数。
> 

```cpp
示例：下函数的实现不太好。
void sum_data( unsigned int num, int *data, int *sum ) 
{
    unsigned int count; 

    *sum = 0; 
    for (count = 0; count < num; count++) 
    { 
        *sum += data[count]; // sum 成了工作变量，不太好。
    } 
} 

若改为如下，则更好些。
void sum_data( unsigned int num, int *data, int *sum )
{ 
    unsigned int count ; 
    int sum_temp; 

    sum_temp = 0; 
    for (count = 0; count < num; count ++) 
    { 
        sum_temp += data[count]; 
    } 

    *sum = sum_temp; 
}
```

### 6-4：函数规模尽量限制在 200 行以内

> 说明：不包括注释和空格行

### 6-5：为简单功能编写函数

> 说明：虽然为仅用一两行就可完成的功能去编函数好象没有必要，但用函数可使功能明确
> 
> 化，增加程序可读性，亦可方便维护、测试。

```cpp
示例：如下语句的功能不很明显。
value = ( a > b ) ? a : b ; 

改为如下就很清晰了。
int max (int a, int b) 
{ 
    return ((a > b) ? a : b); 
} 
value = max (a, b); 

或改为如下
#define MAX (a, b) (((a) > (b)) ? (a) : (b)) 
value = MAX (a, b);
```

### 6-6：避免设计多参数函数

> 目的减少函数间接口的复杂度

### 6-7：非调度函数应降低控制参数

- 尽量只使用数据参数；
- 调度函数是指根据输入的消息类型或控制命令，来启动相应的功能实体（即函数或过程），而本身并不完成具体功能；
- 控制参数是指改变函数功能行为的参数，即函数要根据此参数来决定具体怎样工作。

> 目的是防止函数间的控制耦合。非调度函数的控制参数增加了函数间的控制耦合，很可能使函数间的耦合度增大，并使函数的功能不唯一。
> 

```cpp
示例：如下函数构造不太合理。
int add_sub( int a, int b, unsigned char add_sub_flg ) 
{ 
    if (add_sub_flg == INTEGER_ADD) 
    { 
        return (a + b); 
    } 
    else 
    { 
        return (a b); 
    } 
} 

不如分为如下两个函数清晰。
int add( int a, int b ) 
{ 
    return (a + b); 
}


int sub( int a, int b ) 
{ 
    return (a b); 
}
```

### 6-8：使用动宾词组命名操作函数

- 如果是OOP方法，可只有动词（名词是对象本身）；

```cpp
示例：参照如下方式命名函数。

void print_record( unsigned int rec_ind ) ;

int input_record( void ) ;

unsigned char get_current_color( void ) ;
```

### 6-9：防止函数随机内聚

- 随机内聚：将没有关联或关联很弱的语句放到同一个函数或过程中；

```cpp
示例：如下函数就是一种随机内聚。
void Init_Var( void ) 
{ 
    Rect.length = 0; 
    Rect.width = 0; /* 初始化矩形的长与宽 */ 

    Point.x = 10; 
    Point.y = 10; /* 初始化“点”的坐标 */ 
} 

// 矩形的长、宽与点的坐标基本没有任何关系，故以上函数是随机内聚。
应如下分为两个函数：
void Init_Rect( void ) 
{ 
    Rect.length = 0; 
    Rect.width = 0; /* 初始化矩形的长与宽 */ 
} 
void Init_Point( void ) 
{ 
    Point.x = 10; 
    Point.y = 10; /* 初始化“点”的坐标 */
}
```

### 6-10：扇入扇出设计

- 设计高扇入、合理扇出（小于7）的函数；
- 扇出：一个函数直接调用（控制）其它函数的数目；
- 扇入：有多少上级函数调用它。

**扇出过大：**

- 表明函数过分复杂，需要控制和协调过多的下级函数；
- 一般是由于缺乏中间层次，可适当增加中间层次的函数。

**扇出过小：**

- 如总是1，表明函数的调用层次可能过多，这样不利于程序的阅读和函数结构的分析，并且程序运行时会对系统资源如堆栈空间等造成压力；
- 可把下级函数进一步分解为多个函数，或合并到上级函数中；

**扇入越大：**

- 表明使用此函数的上级函数越多，这样的函数使用效率高，但不能违背函数间的独立性而单纯地追求高扇入。公共模块中的函数及底层函数应该有较高的扇入。

> **较良好的软件结构通常是顶层函数的扇出较高，中层函数的扇出较少，而底层函数则扇入到公共模块中。**

### 6-11：避免使用 BOOL 参数

## 7    可测性

### 7-1：使用断言

- 断言是对某种假设条件进行检查（可理解为若条件成立则无动作，否则应报告），它可以快速发现并定位软件问题，同时对系统错误进行自动报警。

> 断言可以对在系统中隐藏很深，用其它手段极难发现的问题进行定位，从而缩短软件问题定位时间，提高系统的可测性

```cpp
示例：下面是 C 语言中的一个断言，用宏来设计的。（其中 NULL 为 0L）

#ifdef _EXAM_ASSERT_TEST_ // 若使用断言测试
void exam_assert( char * file_name, unsigned int line_no ) 
{ 
    printf( "\n[EXAM]Assert failed: %s, line %u\n", 
        file_name, line_no ); 
    abort( );
} 
#define EXAM_ASSERT( condition ) 
   if (condition) // 若条件成立，则无动作
     NULL; 
   else // 否则报告
     exam_assert( __FILE__, __LINE__ ) 
#else // 若不使用断言测试
#define EXAM_ASSERT(condition) NULL 
#endif /* end of ASSERT */
```

### 7-2：断言非法情况

- 正常运行时不应发生但在调测时有可能发生的非法情况；

### 7-3：不能断言绝对错误

- 用断言来检查最终产品肯定会出现且必须处理的错误情况；

### 7-4：断言函数参数

```cpp
示例：假设某函数参数中有一个指针，那么使用指针前可对它检查，如下。
int exam_fun( unsigned char *str ) 
{     
    EXAM_ASSERT( str != NULL ); // 用断言检查“假设指针不为空”这个条件

    ... //other program code 
}
```

### 7-5：断言程序开发环境（OS/Hardware）

> 对编译器提供的功能及特性假设可用断言检查，原因是软件最终产品（即运行代码或机器码）与编译器已没有任何直接关系，即软件运行过程中（注意不是编译过程中）不会也不应该对编译器的功能提出任何需求。
> 

```cpp
示例：用断言检查编译器的 int 型数据占用的内存空间是否为 2，如下。

EXAM_ASSERT( sizeof( int ) == 2 );
```

### 7-6：正式软件产品中去掉调测代码

> 加快软件运行速度

### 7-7：先处理错误再断言宣布

- 编写防错程序，然后在处理错误之后可用断言宣布发生错误；

> 假如某模块收到通信链路上的消息，则应对消息的合法性进行检查，若消息类别不是通信协议中规定的，则应进行出错处理，之后可用断言报告

```cpp
int msg_handle( unsigned char msg_name, unsigned char * msg ) 
{ 
    switch( msg_name ) 
    { 
    case MSG_ONE: 
        ... // 消息 MSG_ONE 处理
        return MSG_HANDLE_SUCCESS; 

    ... // 其它合法消息处理

    default: 
        ... // 消息出错处理
        ASSERT_REPORT( FALSE ); // “合法”消息不成立，报告
        return MSG_HANDLE_ERROR; 
    } 
}
```

## 8    程序效率

### 8-1：局部效率应为全局效率服务

### 8-2：优化数据结构

- 对数据结构的划分与组织的改进，以及算法优化来提高空间效率；

```cpp
示例：如下记录学生学习成绩的结构不合理。
typedef unsigned char BYTE; 
typedef unsigned short WORD; 
typedef struct STUDENT_SCORE_STRU 
{
    BYTE name[8]; 
    BYTE age; 
    BYTE sex; 
    BYTE class; 
    BYTE subject; 
    float score; 
} STUDENT_SCORE;


因为每位学生都有多科学习成绩，故如上结构将占用较大空间。应如下改进（分为两个结
构），总的存贮空间将变小，操作也变得更方便。

typedef struct STUDENT_STRU 
{
    BYTE name[8]; 
    BYTE age; 
    BYTE sex; 
    BYTE class; 
} STUDENT; 

typedef struct STUDENT_SCORE_STRU 
{ 
    WORD student_index; 
    BYTE subject; 
    float score; 
} STUDENT_SCORE;
```

### 8-3：循环体内工作量最小化

> 说明：应仔细考虑循环体内的语句是否可以放在循环体之外，使循环体内工作量最小，从而提高程序的时间效率
> 

```cpp
示例：如下代码效率不高。
for (ind = 0; ind < MAX_ADD_NUMBER; ind++) 
{ 
    sum += ind; 
    back_sum = sum; /* backup sum */
} 

语句“back_sum = sum;”完全可以放在 for 语句之后，如下。
for (ind = 0; ind < MAX_ADD_NUMBER; ind++) 
{ 
    sum += ind; 
} 
back_sum = sum; /* backup sum */
```

### 8-4：多重循环中将最忙的循环放在最内层

> 说明：减少 CPU 切入循环层的次数

```cpp
示例：如下代码效率不高。
for (row = 0; row < 100; row++) 
{ 
    for (col = 0; col < 5; col++) 
    { 
        sum += a[row][col]; 
    } 
}

可以改为如下方式，以提高效率。
for (col = 0; col < 5; col++) 
{ 
    for (row = 0; row < 100; row++) 
    { 
        sum += a[row][col]; 
    } 
}
```

### 8-5：除法运算进行替代

- 尽量用乘法或其它方法代替除法，特别是浮点运算中的除法；

> 说明：浮点运算除法要占用较多 CPU 资源

```cpp
示例：如下表达式运算可能要占较多 CPU 资源。
#define PAI 3.1416 
radius = circle_length / (2 * PAI);

应如下把浮点除法改为浮点乘法。
#define PAI_RECIPROCAL (1 / 3.1416 ) // 编译器编译时，将生成具体浮点数
radius = circle_length * PAI_RECIPROCAL / 2;
```

## 9    宏

### 9-1：定义表达式时，使用括号

```cpp
示例：如下定义的宏都存在一定的风险。
#define RECTANGLE_AREA( a, b ) a * b 
#define RECTANGLE_AREA( a, b ) (a * b) 
#define RECTANGLE_AREA( a, b ) (a) * (b) 

正确的定义应为：
#define RECTANGLE_AREA( a, b ) ((a) * (b))
```

### 9-2：不允许参数发生变化

```cpp
示例：如下用法可能导致错误。
#define SQUARE( a ) ((a) * (a)) 
int a = 5; 
int b; 
b = SQUARE( a++ ); // 结果：a = 7，即执行了两次增 1。

正确的用法是：
b = SQUARE( a ); 
a++; // 结果：a = 6，即只执行了一次增 1
```
