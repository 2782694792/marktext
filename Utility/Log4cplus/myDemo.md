## 1、简介

    日志系统几乎是每一个实际的软件项目从开发、测试到交付，再到后期的维护过程中极为重要的**查看软件代码运行流程**、**还原错误现场**、**记录运行错误位置及上下文**等的重要依据。一个高性能的日志系统，能够准确记录重要的变量信息，同时又没有冗余的打印导致日志文件记录无效的数据。

    日志，可以记录每一时刻软件的运行情况，记录 error 或者 crash 时的信息（时间、关键变量的值、出错位置、线程等）；另一方面，对于概率性 error 事件，可以在重复测试时通过日志来查询错误复现时候的情况。

> **日志是跟踪和回忆某个时刻或者时间段内的程序行为进而定位问题的一种重要手段**。

## 2、系统设计

    软件运行过程中，需要记录的有什么呢？前述已经提到，**关键变量的值、运行的位置（哪个文件、哪个函数、处在哪一行）、时间、线程号、进程号、自定义提示信息**。

### 2.1 log 设计

#### 2.1.1 log 级别

    Log 级别是什么意思呢？

- 在开发阶段，想尽可能详细地跟踪代码运行过程，可以打印尽可能多的信息到日志文件中；

- 测试过程中，测试部可能不需要这么详细的信息，这时候有的信息可能不必输出到 Log 文件；

- 产品交付客户使用时，为了软件运行更快、客户体验更好，这时候就只需打印关键信息到日志文件了，因为过多的写文件会耗费大量时间，影响软件运行速度。

    故而为 LOG 类定义了如下级别：

```cpp
enum LOGLEVEL
{
    LOG_LEVEL_ALL = 0,
    LOG_LEVEL_TRACE,
    LOG_LEVEL_DEBUG,
    LOG_LEVEL_INFO,
    LOG_LEVEL_WARN,
    LOG_LEVEL_ERROR,
    LOG_LEVEL_FATAL,
    LOG_LEVEL_OFF,
    LOG_LEVEL_COUNT
}
```

    在软件设计中，可以通过某些方法或者预留一些开关来设置 Log 级别，方便在开发、调试、测试和客户现场灵活地调整日志级别，以获取到有用的日志信息。

#### 2.1.2 log 输出位置

    Log 文件可以输出到控制台，也可以输出到指定路径下的某个文件里，也可能有别的需求。比如，开发或调试时，简单的信息直接就打印到软件某个界面上；测试或者交付客户时，最好将日志保存到文件里，这样可以保存尽可能多的信息。因此，进行了如下设计：

```cpp
enum LOGTARGET
{
    LOG_TARGET_NONE    LOGTARGET getLogTarget();  = 0x00,
    LOG_TARGET_CONSOLE   = 0x01,
    LOG_TARGET_FILE      = 0x10
};
```

#### 2.1.3 log 作用域

    一个软件系统，要在哪儿输出日志呢？只要是你想打印日志的地方，任何一个函数、任何一个文件，都应该而且必须可以打印。也就是说这个 log 类的对象（不妨叫做日志记录器）是全局的。

    光是全局的就够了吗？你这个文件里有一个全局的日志记录器，输出日志到 file.log 文件里；另一个文件里也有一个日志记录器，也输出到 file.log 文件里……多个日志记录器同时往一个文件里写日志，这显然不合理。所以还必须保证**日志记录器全局且唯一**！

    怎么保证日志记录器唯一呢？即 Log 类在具体的软件系统中有且仅有一个实例化对象。答案是采用**单例模式**！

> **日志记录器必须是全局的**！
> 
> **日志记录器全局且唯一!**
> 
> **单例模式：日志记录器属于有且仅有的唯一一个实例化对象！**

### 2.2 日志类的设计

```cpp
#pragma once


#include <mutex>
#include <string>
#include <windef.h>

enum LOGLEVEL
{
    LOG_LEVEL_ALL = 0,
    LOG_LEVEL_TRACE,
    LOG_LEVEL_DEBUG,
    LOG_LEVEL_INFO,
    LOG_LEVEL_WARN,
    LOG_LEVEL_ERROR,
    LOG_LEVEL_FATAL,
    LOG_LEVEL_OFF,
    LOG_LEVEL_COUNT
};

enum LOGTARGET
{
    LOG_TARGET_NONE = 0x00,
    LOG_TARGET_CONSOLE = 0x01,
    LOG_TARGET_FILE = 0x10
};

class LOG
{
private:

    void init(LOGLEVEL loglevel, LOGTARGET logtarget);
    void uninit();

    int createFile();

    static LOG * getInstance();

    LOGLEVEL getLogLevel();
    void setLogLevel(LOGLEVEL loglevel);

    LOGTARGET getLogTarget();
    void setLogTarget(LOGTARGET logtarget);

    static int writeLog(
        LOGLEVEL loglevel,
        unsigned char * filename,
        unsigned char * function,
        int linenumber,
        char * format,
        ...
    );

    static void outputToTarget();

private:
    LOG();
    ~LOG();

    static LOG * log;

    // 互斥锁
    static std::mutex log_mutex;

    static std::string logBuffer;

    LOGLEVEL logLevel;

    LOGTARGET logTarget;

    static HANDLE mFileHandle;

};
```

### 2.3 日志类的实现

#### 2.3.1 初始化

```cpp
#pragma once

#include "26Log.h"
#include <handleapi.h>
#include <mutex>

LOG *LOG::log = NULL;
std::string LOG::logBuffer = " ";
HANDLE LOG::m_FileHandle = INVALID_HANLE_VALUE;

LOG::LOG()
{
    init(LOG_LEVEL_ALL, LOG_TARGET_FILE);
}

LOG::~LOG()
{
}

void LOG::init(LOGLEVEL loglevel, LOGTARGET logtarget)
{
    setLogLevel(loglevel);
    setLogTarget(logtarget);
    createFile();
}

void LOG::uninit()
{
    if (INVALID_HANDLE_VALUE != m_FileHandle)
    {
        CloseHandle(m_FileHandle);
    }
}

LOG *LOG::getInstance()
{
    if (NULL == log)
    {
        log_mutex.lock();
        if (NULL == log)
        {
            log = new LOG();
        }
        log_mutex.unlock();
    }
    return log;
}

LOGLEVEL LOG::getLogLevel()
{
    return this->logLevel;
}

void LOG::setLogLevel(LOGLEVEL iLogLevel)
{
    this->logLevel = iLogLevel;
}

LOGTARGET LOG::getLogTarget()
{
    return this->logTarget;
}

void LOG::setLogTarget(LOGTARGET iLogTarget)
{
    this->logTarget = iLogTarget;
}
```

    初始化工作设置了日志的级别和输出位置（日志级别和输出位置的 setter、getter 方法）。

    函数 createFile( ) 是创建日志文件位置，并获取日志文件的句柄 mFileHandle。代码如下：

```cpp
int LOG::createFile()
{
    // 当前路径
    TCHAR fileDirectory[_MAX_DIR];
    GetCurrentDirectory(_MAX_DIR, fileDirectory);

    // log 文件路径
    TCHAR logFileDirectory[_MAX_DIR];
    _stprintf_s(logFileDirectory, _T("%s\\Log\\"), fileDirectory);
    if (_taccess(logFileDirectory, 0) == -1) // 确定文件或文件夹的访问权限  _access  《#include <io.h> 》
    {
        _tmkdir(logFileDirectory); // 文件夹不存在重新创建
    }

    // fixme: 目录路径与文件
    WCHAR moduleFileName;
    GetModuleFileName(NULL, moduleFileName, MAX_PATH); // 程序当前目录
    PWCHAR p = wcsrchr(moduleFileName, T('\\')); // 返回指向路径中 \ 字符的最后一个位置的指针
    p++;
    for (int i = _tcslen(p); i > 0; i--) // _t 宏处理（取决于字符集），映射字符，返回中字符串的长度字节
    {
        if (p[i] == _T('.')) // fixme: 后缀名处理
        {
            p[i] = _T('\0');
            break;
        }
    }

    WCHAR logFileName[MAX_PATH];
    _stprintf_s(logFileName, _T("%s%s.log"), logFileDirectory, p);

    mFileHandle = CreateFile(
        logFileName,
        GENERIC_READ | GENERIC_WRITE,
        FILE_SHARE_READ,
        NULL,
        OPEN_ALWAYS,
        FILE_ATTRIBUTE_NORMAL,
        NULL);
    if (INVALID_HANDLE_VALUE == mFileHandle)
    {
        return -1;
    }
    return 0;
}
```

其中，需要介绍的是下述函数：

- GetCurrentDirectory：在一个缓冲区中装载当前目录
- _stprintf_s：将若干个参数按照 format 格式存到 buffer 中；
- _taccess：判断文件是否存在，返回 0 表示该文件存在，返回 -1 表示文件不存在或者该模式下没有访问权限；
- _tmkdir：创建一个目录；
- GetModuleFileName：获得指定句柄下的程序目录路径；
- wcsrchr：返回指向路径中指定字符的最后一个位置的指针。

#### 2.3.2 写日志

实现方法 writeLog :

```cpp
static int LOG::writeLog(
    LOGLEVEL loglevel,
    unsigned char *filename,
    unsigned char *function,
    int linenumber,
    char *format,
    ...)
{
    // yyyy-mm-dd HH:MM:SS.sss
    char timeBuffer[128];
    int ret = getSysTime(timeBuffer);
    m_logBuffer += string(timeBuffer);

    char * logLevel = m_ccLogLevel[loglevel];

    // [进程号][线程号][Log级别][文件名][函数名:行号]
    char locInfo[128];
    char * format2 = "[PID:%4d][TID:%4d][%s][-%s][%s:%4d]";
    int ret = printfToBuffer(locInfo, 128, format2
                , GetCurrentProcessId()
                , GetCurrentThreadId()
                , logLevel
                , filename
                , function
                , linenumber );
    m_logBuffer += string(locInfo);

    // describe content
    char content[256];
    va_list ap;
    va_start(ap, format);
    vsnprintf_s(content, 256, format, ap);
    va_end(ap);

    m_logBuffer += string(content);
    m_logBuffer += string("\n");

    // 输出文件
    outputToTarget();

    return 0;
}
```

#### 2.3.3 输出日志

实现方法 outputToTarget :

```cpp
static void LOG::outputToTarget()
{
    if (LOG::getInstance()->getLogTarget() & LOG_TARGET_FILE)
    {
        SetFilePointer(m_fileHandle, 0, NULL, FILE_END);
        DWORD dwByteWritten = 0;
        WriteFile(m_fileHandle, m_logBuffer.c_str(), m_logBuffer.length()
            , &dwByteWritten, 0);
        FlushFileBuffers(m_fileHandle);
    }
    if (LOG::getInstance()->getLogTarget() & LOG_TARGET_CONSOLE)
    {
        printf("%s", m_logBuffer.c_str());
    }

    m_logBuffer.clear();
}
```

注意：

- SetFilePointer：将文件指针移动到文件指定的位置；
- FlushFileBuffers：把写文件缓冲区的数据强制写入磁盘；
- 当文件内容过多时，需要进行另存和翻滚重写。

# 3、测试

# 4、多线程环境

## 4.1 测试多线程

    通过简单的多线程环境应用，对上述日志系统进行测试，测试代码如下：

```cpp
#include <stdio.h>
#include "Log.h"

// 线程数量
#define THREAD_NUM 2
// 全局资源变量
int g_num = 0;

// 响应回调函数
unsigned int __stdcall func( void * pPM )
{
    LOG_INFO( "enter" );
    Sleep( 50 );
    g_num++;
    LOG_INFO( "g_num = %d", g_num );
    LOG_INFO( "exit" );
    return 0;
}

int main( )
{
    LOG::getInstance( )->init( LOGLEVEL::LOG_LEVEL_FATAL
        , LOGTARGET::LOG_TARGET_CONSOLE );

    /// 线程句柄
    HANDLE handle[ THREAD_NUM ];

    // 线程编号
    int threadNum = 0;
    while ( threadNum < THREAD_NUM )
    {
        handle[ threadNum ] = (HANDLE)_beginthreadex( NULL, 0, func
            , NULL, 0, NULL );
        //! 等子线程接收到参数时，主线程可能改变了这个值
        threadNum++;
    }
    // 确保子线程已全部执行完毕
    WaitForMultipleObjects( THREAD_NUM, handle, true, INFINITE );

    return 0;
}
```

![](D:\Download\MarkText\workspace\image\2022-10-29-09-05-37-image.png)

    上述代码中，当开启了 5 个线程的时候，（VS 2019 —— Debug）系统下直接中断弹出断点；当开启了 2 个线程的时候，一定几率下才得以运行成功，理论上 TID 应该有 2 个不同的值，但实际上由以上输出可以看到，仅有 2 个同线程 TID 运行成功。

## 4.2 问题

    仔细查看第一行、第三、第四与第五、第六行的数据比较，可以发现第一行的数据异常增多，而第二行数据明显减少，好比一次性运行了两次或者运行没完全就结束，也就是说，此刻运行的同时，其他线程都跑到函数 writeLog 里写 Log，由此造成了原先的 logBuffer 缓冲区数据改动。

> 根本问题是多个线程在同一时刻访问同一个资源。

    所以针对多线程问题，需要做到多线程环境下的共享资源互斥。

## 4.3 线程安全

    通过互斥锁便可以对线程与线程之间进行互斥阻塞，从而达到线程安全、同一时刻不访问同一个共享资源。而这里将使用另一种方法：临界区。

在 Log 类成员里声明一个 CRITICAL_SECTION 对象 criticalSection ，进行初始化：

```cpp
InitializeCriticalSection(&criticalSection);
```

同时，要在释放资源时对临界区进行释放处理，（释放意味着结束多线程）如下：

```cpp
DeleteCriticalSection(&criticalSection); // 析构函数下实现
```

确定了临界区，接下来，将临界区设置在临界函数 writeLog 上，以控制临界区的进入和离开：

```cpp
int LOG::writeLog(...)
{
    int ret = 0;

    EnterCriticalSection(&criticalSection);

        // do something

    LeaveCriticalSection(&criticalSection);

    return 0;
}
```

> 注：在 LeaveCriticalSection 之后再 DeleteCriticalSection。

# 5、多线程测试

可以看到，日志完整记录了每个线程的运行过程（线程号 TID 不同）：

![](D:\Download\MarkText\workspace\image\2022-10-31-09-19-11-image.png)
