# log4cplus _ 2.0.8

# 1、简易输出控制台

```cpp
#include <log4cplus/initializer.h>
#include <log4cplus/configurator.h>
#include <log4cplus/logger.h>
#include <log4cplus/loggingmacros.h>

int main(int argc，char*argv[])
{
    // 初始化
    log4cplus::Initializer initializer;
    // 配置器对象实例化
    log4cplus::BasicConfigurator config;
    // 触发默认配置，可做配置文件 .config .property
    config.configure( );
    // 日志输出实例化
    log4cplus::Logger LogConsole = log4cplus::Logger::getInstance(
        LOG4CPLUS_TEXT( "main" ) );
    LOG4CPLUS_DEBUG( LogConsole, "this is debug log: "  
        << LOG4CPLUS_STRING_TO_TSTRING(fun()) );

    return 0;
}
```

# 2、控制台输出 SimpleLayout

头文件：

```cpp
#include <log4cplus/initializer.h>
#include <log4cplus/consoleappender.h> 
#include <log4cplus/logger.h>
#include <log4cplus/loggingmacros.h>
```

源文件：

```cpp
//! 1、初始化 initialize
log4cplus::initialize( );
//! 2、输出介质实例化 appender
log4cplus::SharedAppenderPtr  _consoleAppender( 
    new log4cplus::ConsoleAppender );
//! 3、输出格式实例化 layout
log4cplus::SimpleLayout * _consoleLayout = new log4cplus::SimpleLayout( );
//! 4、layout 输出介质对象绑定到 appender 输出格式对象
//!? 省略 layout 输出介质对象绑定到 appender 输出格式对象，
//!? 简单布局器会绑定到 logger 日志输出对象
_consoleAppender->setLayout( std::unique_ptr<log4cplus::Layout>( 
    _consoleLayout ) );
//! 5、日志输出实例化 logger
log4cplus::Logger logger = log4cplus::Logger::getInstance( 
    LOG4CPLUS_TEXT( "test" ) );
//! 6、日志等级 loglevel、输出介质绑定 addappender
logger.setLogLevel( log4cplus::DEBUG_LOG_LEVEL );
logger.addAppender( _consoleAppender );

LOG4CPLUS_DEBUG( logger, "test : " << strFun().c_str( ) );
```

# 3、文件输出 PatternLayout

头文件：

```cpp
#include <log4cplus/initializer.h>
#include <log4cplus/fileappender.h> 
#include <log4cplus/logger.h>
#include <log4cplus/loggingmacros.h>
```

源文件：

```cpp
//! 1、初始化 initialize
log4cplus::initialize( );
//! 2、输出介质实例化 appender
//? 指定路径需存在，否则存放在 cpp 文件目录下
log4cplus::SharedAppenderPtr _fileAppender( 
    new log4cplus::FileAppender(LOG4CPLUS_TEXT( "test.log" )
    , std::ios_base::app & std::ios_base::ate ) );
//! 3、输出格式实例化 layout
log4cplus::tstring pattern = LOG4CPLUS_TEXT( 
    "[%D{%Y-%m-%d %H:%M:%S,%Q}] [%t] %-5p - %m%n[%p]" );
log4cplus::PatternLayout * _patternLayout 
    = new log4cplus::PatternLayout( pattern );
//! 4、layout 输出介质对象绑定到 appender 输出格式对象
//!? 省略 layout 输出介质对象绑定到 appender 输出格式对象，
//!? 简单布局器会绑定到 logger 日志输出对象
//? _patternLayout 被调用后会被释放，不可再次调用
_fileAppender->setLayout( 
    std::unique_ptr<log4cplus::Layout>( _patternLayout ) );
//! 5、日志输出实例化 logger
log4cplus::Logger logger = log4cplus::Logger::getInstance( 
    LOG4CPLUS_TEXT( "test" ) );
//! 6、日志等级、输出介质绑定
logger.setLogLevel( log4cplus::DEBUG_LOG_LEVEL );
logger.addAppender( _fileAppender );

LOG4CPLUS_DEBUG( logger, "test : " << strFun( ).c_str( ) );
```

# 4、服务器输出

```cpp
#include <log4cplus/initializer.h>
#include <log4cplus/socketappenderr.h> 
#include <log4cplus/logger.h>
#include <log4cplus/loggingmacros.h>

int main()
{
    //用 Initializer 类进行初始化
    log4cplus::Initializer initializer;

    //第1步：建立 SocketAppender
    log4cplus::SharedAppenderPtr appender(
        new log4cplus::SocketAppender(LOG4CPLUS_TEXT("localhost")
            , 32015, LOG4CPLUS_TEXT("test")));

    //第2步：设置 Appender 的名称，SocketAppender 不须要设置输出格式
    appender->setName(LOG4CPLUS_TEXT("logserver"));

    //第3步：得到一个 Logger 实例，并设置其日志输出等级阈值
    log4cplus::Logger logger = log4cplus::Logger::getInstance( 
        LOG4CPLUS_TEXT ("test"));
    logger.setLogLevel(log4cplus::INFO_LOG_LEVEL);

    //第4步：为 Logger 实例添加 Appender
    logger.addAppender(appender);

    //第5步：使用宏将日志输出
    LOG4CPLUS_INFO(logger, LOG4CPLUS_TEXT("Hello world"));

    return 0;
}
```
