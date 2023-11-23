# 一、头文件

    主要类说明：

| 类名        | 说明                                                                                     |
| --------- | -------------------------------------------------------------------------------------- |
| Filter    | 过滤器，过滤输出消息                                                                             |
| Layout    | 布局器，控制输出消息的格式<br/>每一个Appender须要设置一个Layout。                                             |
| Appender  | 挂接器，与布局器和过滤器紧密配合，将特定格式的消息过滤后输出到所挂接的设备终端如屏幕，文件等。<br/>一个Logger可添加多个Appender，从而向多个位置输出日志。 |
| Logger    | 记录器，保存并跟踪对象日志信息变更的实体，当需要对一个对象进行记录时，就需要生成一个logger。                                      |
| Hierarchy | 分类器，层次化的树型结构，用于对被记录信息的分类，层次中每一个节点维护一个logger的所有信息                                       |
| LogLevel  | 优先权，包括TRACE, DEBUG, INFO,WARNING, ERROR, FATAL。                                        |

    <img src="../../image cache/2022-11-29-16-28-31-image.png" title="" alt="" data-align="center">

主要头文件如下：

- 获取表示记录句柄的类：

```cpp
#include <log4cplus/logger.h>
```

- 声明日志记录宏。还声明了若干个标准日志级别：

```cpp
#include <log4cplus/loggingmacros.h>
```

- 声明类 BasicConfigurator ：

```cpp
#include <log4cplus/configurator.h>
```

- 声明类 Initializer ：

```cpp
 #include <log4cplus/initializer.h>
```

> 注 : 在 log4cplus2 中，上述类都已经包含在头文件 log4cplus.h 中
> 
> ```cpp
> #include <log4cplus/log4cplus.h>
> ```

# 二、初始化 Initializer

    实例化该类会初始化 log4cplus Initializer ：

```cpp
log4cplus::Initializer initializer;
```

    该类还维护一个引用计数，可被实例化多次，当引用计数达到 0 时，最后一个实例被销毁，会关闭 log4cplus 内部，解除初始化后，无法重新初始化。

# 三、基本配置 BasicConfigurator

    使用简单的布局配置根记录器 ： ConsoleAppender

```cpp
log4cplus::BasicConfigurator config;
config.configure();
```

    获取名为 main 的记录器的记录器句柄：

```cpp
log4cplus::Logger logger = log4cplus::Logger::getInstance(
    LOG4CPLUS_TEXT("main"));
```

> 调用宏来记录输出消息，将消息输出主记录器。
> 
> 记录消息将从主记录器传播到根记录器，其连接了一个用于在控制台上打印的日志。
> 
> LOG4CPLUS_WARN() 在内部对应宏使用字符串流格式化输出。

# 四、取消初始化 shutdown

    取消初始化（deinitialize），并在退出后释放所有分配的资源。

```cpp
Logger::shutdown()
```

# 五、日志记录宏

    宏在 hood 下使用 C++ 字符串流。

# 六、日志级别

    优先级由低到高：

- NOT_SET_LOG_LEVEL : 接受缺省的LogLevel，如果有父logger则继承；

- ALL_LOG_LEVEL : 所有log信息输出；

- TRACE_LOG_LEVEL : trace【在线调试】

- DEBUG_LOG_LEVEL : debug【终端查看、在线调试】

- INFO_LOG_LEVEL : info【报告程序进度和状态信息】

- WARN_LOG_LEVEL : warning【一般警告信息】

- ERROR_LOG_LEVEL : error【状态错误】

- FATAL_LOG_LEVEL : fatal【致命错误】

- OFF_LOG_LEVEL : 关闭所有 log 信息输出。

> 如果定义了重名的多个 logger，对其中任何一个的修改都会同时改变其他 logger。

# 七、输出位置 Appender

    log4cplus默认将输出到控制台，提供ConsoleAppender用于操作。

    log4cplus还提供了三个类用于文件操作：

- FileAppender类、

- RollingFileAppender类、

- DailyRollingFileAppender类。

    Appender 会注册到 Logger 中，Logger 写日志时，通过`继承机制遍历所有注册`到它本身和其父节点的 Appender（additivity 为 true 时），调用 doAppend() 方法，实现日志写入。

    在 doAppend 中，若当前 Appender 注册了 Filter，则还会判断当前日志`是否通过 Filter 过滤`。

    通过了过滤后，如果当前 Appender 继承自 SkeletonAppender，还会检查当前日志级别是否要比当前 Appender `本身日志级别阀门`要大。

    所有这些都通过后，才会将 LoggingEvent 实例传递给 Layout 实例以格式化成一行日志信息，最后写入目的地，其中，任何出现的错误都由 ErrorHandler 字段来处理。

## 7.1 ConsoleAppender

    控制台输出

## 7.2 FileAppender

    文件输出：

```cpp
FileAppender::FileAppender( const tstring& filename_,
    std::ios_base::openmode mode_, bool immediateFlush_,
    bool createDirs_)
```

> - filename : 文件名
> 
> - mode : 文件类型（app、ate、binary、in、out、trunc），缺省是 trunc，表示将先前文件删除
> 
> - immediateFlush : 缓冲刷新标志，true —— 每向文件写一条记录就刷新一次缓存，否则直到 FileAppender 被关闭或文件缓存已满才更新文件
> 
> - createDirs : 是否创建目录

```cpp
#include <log4cplus/log4cplus.h>

int main()
{
    //! 类初始化
    // 用Initializer类进行初始化
    log4cplus::Initializer initializer;

    //! FileAppender 建立
    //第1步：建立 Appender
    log4cplus::SharedAppenderPtr appender(
        new log4cplus::FileAppender(
            LOG4CPLUS_TEXT("E:\\test.log")));

    //! 设置 Appender 属性
    //第2步：设置Appender的名称和输出格式（SimpleLayout）
    appender->setName(LOG4CPLUS_TEXT("console"));
    log4cplus::tstring pattern =  LOG4CPLUS_TEXT(
        "%D{%m/%d/%y %H:%M:%S,%Q} [%t] %-5p %c - %m [%l]%n");
    appender->setLayout(std::unique_ptr<log4cplus::Layout>(
        new log4cplus::PatternLayout(pattern)));

    //! 获取 logger 实例并设置输出等级
    //第3步：得到一个Logger实例，并设置其日志输出等级阈值
    log4cplus::Logger logger = log4cplus::Logger::getInstance(
        LOG4CPLUS_TEXT("test"));
    logger.setLogLevel(log4cplus::INFO_LOG_LEVEL);

    //! 添加对应的输出 Appender
    //第4步：为Logger实例添加ConsoleAppender
    logger.addAppender(appender);

    //! 打日志
    //第5步：使用宏将日志输出
    LOG4CPLUS_INFO(logger, LOG4CPLUS_TEXT("Hello world"));

    return 0;
}
```

    同时输出到文件和控制台，在文件输出的对应步骤上进行添加控制台输出控制：

```cpp
//! 建立输出 ConsoleAppender
log4cplus::SharedAppenderPtr consoleAppender(new log4cplus::ConsoleAppender); 
//! 设置 Appender 名称和输出格式
consoleAppender->setName(LOG4CPLUS_TEXT("console"));
consoleAppender->setLayout(std::unique_ptr<log4cplus::Layout>( 
    new log4cplus::SimpleLayout()));
//! logger 实例建立、设置等级阈值
//! logger 添加 ConsoleAppender
```

## 7.3 RollingFileAppender

    实现滚动转储的文件操作功能。

- 根据预先设定的大小来决定是否转储，当超过该大小，后续 log 信息会另存到新文件中；

- 除了定义每个记录文件的大小之外，还要确定 RollingFileAppender 类对象构造时最多需要多少个这样的记录文件（maxBackupIndex + 1）；

- 当存储文件数目超过指定的数量时，会删除最早生成的文件来进行维护。

```cpp
RollingFileAppender(const log4cplus::tstring& filename, // 文件名称
    long maxFileSize = 10*1024*1024, // 10 MB ，文件的最大尺寸
    int maxBackupIndex = 1, // 最大记录文件数
    bool immediateFlush = true, // 缓冲刷新标志
    bool createDirs = false); // 创建目录
```

## 7.4 DailyRollingFileAppender

    根据频度来决定是否转储的文件转储功能。

- 根据预先设定的频度来决定是否转储，当超过该频度，后续 log 信息会另存到新文件中；

- 频度 : 每月、每周、每日、每两天、每时、每分；
  
  ```cpp
  enum DailyRollingFileSchedule { MONTHLY, WEEKLY, DAILY, 
  TWICE_DAILY, HOURLY, MINUTELY}; 
  ```

```cpp
DailyRollingFileAppender(const log4cplus::tstring& filename, 
    DailyRollingFileSchedule schedule = DAILY, // 存储频度 
    bool immediateFlush = true, // 缓冲刷新标志
    int maxBackupIndex = 10, // 最大记录文件数
    bool createDirs = false); // 创建目录
```

## 7.5 SocketAppender

    实现 C/S 方式的日志记录，用于支持重定向到远程服务器。

### （1）客户端程序

    定义一个 SocketAppender 类型的挂接器：

```cpp
ServerSocket serverSocket(port);
```

    调用 accept 函数创建一个新的 socket 与客户端建立连接：

```cpp
Socket sock = serverSocket.accept();
```

    利用该 sock 进行数据 read/write ：

```cpp
 SocketBuffer msgSizeBuffer(sizeof(unsigned int));
 if(!clientsock.read(msgSizeBuffer)){
     return;
 }
 unsigned int msgSize = msgSizeBuffer.readInt(); 
 SocketBuffer buffer(msgSize);
 if(!clientsock.read(buffer)){
     return;
 }
```

    获取读到的数据，需要将 SocketBuffer 存放的内容转换成 InternalLoggingEvent 格式：

```cpp
log4cplus::spi::InternalLoggingEvent event = readFromBuffer(buffer);
```

    输出：

```cpp
Logger logger = Logger::getInstance(event.getLoggerName());
logger.callAppenders(event);
```

> read/write 是按照阻塞方式实现，对其调用直到满足所接收或发送的个数才返回。

# 八、布局设置

    log4cplus通过布局器（Layouts）来控制输出的格式，log4cplus提供了三种类型的Layouts，分别是SimpleLayout、PatternLayout、和TTCCLayout。

## 8.1 SimpleLayout

    一种简单格式的布局器，在输出的原始信息之前加上一个 LogLevel 和一个 “-”，如果初始化没有将布局器附加到挂接器，则默认使用 SimpleLayout。

## 8.2 PatternLayout

    一种有词法分析功能的模式布局器，类似 C 语言的1Print() 函数，能对预定义的转换标识符进行解析，转换成特定格式输出。

    PatternLayout 支持的转换标识符主要包括：

（1）`%%`，转义为 % ；

（2）`%c` ，输出 logger 名称（如输出 “test_logger.subtest”），也可以控制 logger 的显示层次，如 `%c{1}` 输出 “test_logger”;

（3）`%D` ，显示本地时间，输出:“2004-10-16 18:55:45”，`%d` 显示标准时间，输出 “2004-10-16 10:55:45”，东八区，相差八个小时；

> 可以通过 `%d{…}` 定义更详细的显示格式，比如 %d{%H:%M:%s} 表示要显示小时:分钟：秒。大括号中可显示的预定义标识符如下：

| `%a` – 表示礼拜几，英文缩写形式，比如"Fri" <br/><br/>`%A` – 表示礼拜几，比如"Friday" <br/><br/>`%b` – 表示几月份，英文缩写形式，比如"Oct" <br/><br/>`%B` – 表示几月份，“October” <br/><br/>`%c` – 标准的日期＋时间格式，如 “Sat Oct 16 18:56:19 2004” <br/><br/>`%d` – 表示今天是这个月的几号(1-31)“16” <br/><br/>`%H` – 表示当前时刻是几时(0-23)，如 “18” <br/><br/>`%I` – 表示当前时刻是几时(1-12)，如 “6” <br/><br/>`%j` – 表示今天是哪一天(1-366)，如 “290”<br/><br/>`%m` – 表示本月是哪一月(1-12)，如 “10” <br/><br/>`%M` – 表示当前时刻是哪一分钟(0-59)，如 “59” <br/><br/>`%p` – 表示现在是上午还是下午， AM or PM <br/><br/>`%q` – 表示当前时刻中毫秒部分(0-999)，如 “237” <br/><br/>`%Q` – 表示当前时刻中带小数的毫秒部分(0-999.999)，如 “430.732” <br/><br/>`%S` – 表示当前时刻的多少秒(0-59)，如 “32” <br/><br/>`%U` – 表示本周是今年的第几个礼拜，以周日为第一天开始计算(0-53)，如 “41” <br/><br/>`%w` – 表示礼拜几，(0-6, 礼拜天为0)，如 “6” <br/><br/>`%W` – 表示本周是今年的第几个礼拜，以周一为第一天开始计算(0-53)，如 “41” <br/><br/>`%x` – 标准的日期格式，如 “10/16/04” <br/><br/>`%X` – 标准的时间格式，如 “19:02:34” <br/><br/>`%y` – 两位数的年份(0-99)，如 “04” <br/><br/>`%Y` – 四位数的年份，如 “2004” <br/><br/>`%Z` – 时区名，比如 “GMT” |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

（4）"%F"，输出当前记录器所在的文件名称；

（5）"%L"，输出当前记录器所在的文件行号；

（6）"%l"，输出当前记录器所在的文件名称和行号，比如 "main.cpp:51"；

（7）"%m"，输出原始信息；

（8）"%n"，换行符；

（9）"%p"，输出 LogLevel；

（10）"%t"，输出记录器所在的线程 ID；

（11）"%x"，嵌套诊断上下文NDC (nested diagnostic context) 输出，从堆栈中弹出上下文信息，NDC可以用对不同源的log信息（同时地）交叉输出进行区分；

（12）格式对齐，比如std::string pattern ="%-10m"时表示左对齐，宽度是10。

## 8.3 TTCCLayout

    TTCCLayout 是在 PatternLayout 基础上发展的一种缺省的带格式输出的布局器， 其格式由时间，线程ID，Logger和NDC 组成。

# 九、实现步骤

    有七个基本步骤：

1. 初始化；

2. 实例化一个封装了输出介质的 appender 对象；

3. 实例化一个封装了输出格式的 layout 对象；

4. 将 layout 对象绑定到 appender 对象；如省略，简单布局器 SimpleLayout 对象会绑定到logger；

5. 实例化一个封装了日志输出 logger 对象,并调用其静态函数 getInstance() 获得实例；

6. 将 appender 对象绑定到 logger 对象；

7. 设置 logger 优先级，如省略，各种有限级的日志都将被输出。

# 十、注意事项

1. 使用 ConsoleAppender() 默认参数，那么控制台消息不会实时输出：

```cpp
log4cplus::SharedAppenderPtr append_console(new log4cplus::ConsoleAppender(false,true));
```

2. 输出数字、字符、字符串：

```cpp
LOG4CPLUS_INFO(logger, "Entering loop #" << 123);

LOG4CPLUS_INFO(logger, "Entering loop #" << 'i');

LOG4CPLUS_INFO(logger, "Entering loop #" << LOG4CPLUS_C_STR_TO_TSTRING("dsa"));
```
