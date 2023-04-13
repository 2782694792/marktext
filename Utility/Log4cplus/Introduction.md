# 一、log4cplus（日志库）

## 1.1 介绍

    开源日志系统，由 Java log4j 衍生而来，受 Apache 软件许可证保护，作者是Tad E. Smith。

- 具有线程安全、灵活、多粒度控制的特点，通过将日志划分优先级使其可以面向程序调试、运行、测试和维护等生命周期。

- 可以选择将日志输出到控制台、文件、NT 事件日志、甚至是远程服务器；通过指定策略对日志进行定期备份等等。

## 1.2 其它

- log4cxx：

    Java 著名的 Log4j 的 C++ 移植版，用于为 C++ 程序提供日志功能，以便开发者对目标程序进行调试和审计。Apache下的孵化项目，不能独立编译，依赖于APR，编译比较麻烦。

- log4cplus：

    简单易用的 C++ 日志记录 API，它提供了对日志管理和配置的线程安全、灵活和任意粒度控制（基于 Log4j）。受Apache Software License保护，版本更新频繁。

- Log4cpp：

    C++ 类库，可灵活地记录到文件、syslog、IDSA 和其他目的地（基于 Log4j），也不依赖与第三方库，与log4cplus功能类似，版本更新不频繁。

- spdlog：

    一个快速的 C++ 日志库，只包含头文件，兼容 C++11。

- ……

# 二、下载

## 2.1 网址

-     log4cplus 官方（SourceForge）下载地址：

> [log4cplus download | SourceForge.net](https://sourceforge.net/projects/log4cplus/)

    许可证：Apache许可证2.0版

- log4cplus GitHub下载地址：

> https://github.com/log4cplus/log4cplus

## 2.2 版本

（1）支持VS2013编译

    log4cplus-1.2.2（2019年03月发布）源码下载：

> https://sourceforge.net/projects/log4cplus/files/log4cplus-stable/1.2.2/

（2）最新发布的稳定版本

    log4cplus-2.0.8（2022年07月发布）源码下载：

> [log4cplus download | SourceForge.net](https://sourceforge.net/projects/log4cplus/)

# 三、编译与配置

## 3.1 运行解决方案

    打开Visual Studio，进入 log4cplus-2.0.8\msvc10目录下，运行log4cplus.sln解决方案。

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-28-16-59-29-image.png" title="" alt="" data-align="center">

## 3.2 项目配置

    选择 log4cplus 项目，右键——>属性——>配置属性——>常规——>字符集，选择 Unicode 字符集，如下图所示。

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-28-17-00-07-image.png" title="" alt="" data-align="center">

    编译完成后，会在 log4cplus-2.0.8\msvc10\x64\bin.Release 文件夹下生成我们需要的 log4cplus.lib 和 log4cplus.dll 两个文件（release 版），如下图所示。生成的文件放到我们的项目文件夹下（版本号_编译环境 系统位数）或者记住文件位置，方便项目属性进行配置。

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-28-17-00-42-image.png" title="" alt="" data-align="center">

## 3.3 目标程序配置

    将 log4cplus-2.0.8 目录下的 include 文件夹同样也是放入到我们的目标程序所创建的文件夹当中，如下，在项目属性设置该头文件包含目录：

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-28-16-58-27-image.png" title="" alt="" data-align="center">

![](D:\Download\MarkText\workspace\image\2022-11-28-16-56-24-image.png)

    设置 lib 文件的库目录以及将 lib 文件填入附加依赖项，如下：

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-28-16-57-02-image.png" title="" alt="" data-align="center">

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-28-16-55-18-image.png" title="" alt="" data-align="center">

或者进行头文件添加 lib 附加依赖项：

```cpp
#pragma comment(lib, "log4cplusU.lib")
```

生成的 dll 文件需放在项目同路径下：

<img src="file:///D:/Download/MarkText/workspace/image/2022-11-28-16-54-43-image.png" title="" alt="" data-align="center">

# 四、配置文件

    配置文件 logconfig.property（也可直接 cpp 文件编写跳过该文件配置）：

```git
# RootLogger配置格式：log4cplus.rootLogger=[LogLevel],appenderName1
#     ,appenderName2,...,如此处LogLevel为DEBUG，appenderName1为 rootLogger

log4cplus.rootLogger=DEBUG,rootLogger
# 设置日志追加到文件尾 
log4cplus.appender.rootLogger=log4cplus::RollingFileAppender
# 设置直接在控制台输出 
log4cplus.appender.rootLogger=log4cplus::ConsoleAppender 
# 设置日志文件大小 
log4cplus.appender.rootLogger.MaxFileSize=100MB

#设置生成日志最大个数 
log4cplus.appender.rootLogger.MaxBackupIndex=2 

#设置输出日志路径 
log4cplus.appender.rootLogger.File=rootLogger.log 

log4cplus.appender.rootLogger.layout=log4cplus::PatternLayout 

#设置日志打印格式 
log4cplus.appender.rootLogger.layout.ConversionPattern=%D:%d{%Q}|%p|%t|%l|%m|%n

#设置日志级别范围
log4cplus.appender.rootLogger.filters.1=log4cplus::spi::LogLevelRangeFilter 
log4cplus.appender.rootLogger.filters.1.LogLevelMin=TRACE 
log4cplus.appender.rootLogger.filters.1.LogLevelMax=FATAL 
log4cplus.appender.rootLogger.filters.1.AcceptOnMatch=true 
log4cplus.appender.rootLogger.filters.2=log4cplus::spi::DenyAllFilter 

#==============================================================================
log4cplus.logger.test=DEBUG,test 
log4cplus.appender.test=log4cplus::RollingFileAppender 
log4cplus.appender.test.File=test.log 
log4cplus.appender.test.layout=log4cplus::PatternLayout 
log4cplus.appender.test.layout.ConversionPattern=%D:%d{%Q}|%p|%t|%l|%m|%n 
log4cplus.appender.test.filters.1=log4cplus::spi::LogLevelRangeFilter 
log4cplus.appender.test.filters.1.LogLevelMin=TRACE 
log4cplus.appender.test.filters.1.LogLevelMax=FATAL 
log4cplus.appender.test.filters.1.AcceptOnMatch=true
log4cplus.appender.test.filters.2=log4cplus::spi::DenyAllFilter
log4cplus.appender.test.MaxFileSize=100MB
log4cplus.appender.test.MaxBackupIndex=2

# 如何防止自定义的logger和root重复写入日志
log4cplus.additivity.test=false
```

# 五、日志等级

    不同版本中日志的分级也有所区别，其中包含：TRACE, DEBUG, INFO, WARNING, ERROR, FATAL；还有两个特殊等级：ALL、OFF，全部打开和关闭。

> 常用四个级别：ERROR > WARN > INFO > DEBUG

## 1.1 TRACE

    在线调试：既不打印到终端也不输出到文件。

## 1.2 DEBUG

    终端查看、在线调试：默认会打印到终端，但是不会归档到日志文件。

## 1.3 INFO

    报告程序进度和状态信息：一般信息是易过性的，不会大量反复输出。

## 1.4 WARNING

    警告信息，程序处理中遇到非法数据或者某种可能的错误。

## 1.5 ERROR

    状态错误：该错误发生后可以保持在一定程序上继续运行。

## 1.6 FATAL

    致命错误：程序遇到错误，必须马上终止运行。

# 六、测试代码

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
    // 触发默认配置    
    config.configure( );

    log4cplus::Logger LogConsole = log4cplus::Logger::getInstance(
        LOG4CPLUS_TEXT( "main" ) );
    LOG4CPLUS_DEBUG( LogConsole, "this is debug log: "  
        << LOG4CPLUS_STRING_TO_TSTRING(fun()) );
    return 0;

    /*
    // step 1: 设置输出对象 console
    SharedObjectPtr<Appender> _append (new ConsoleAppender());
    _append->setName(LOG4CPLUS_TEXT("append for test"));
    // step 2: 设置日志输出格式对象
    //std::string pattern = "%d{%m/%d/%y %H:%M:%S}  - %m [%l]%n";
    log4cplus::tstring pattern = LOG4CPLUS_TEXT("%d{%m/%d/%y %H:%M:%S}  - %m [%l]%n");
    std::auto_ptr<Layout> _layout(new PatternLayout(pattern));
    // step 3: 绑定屏幕和格式对象
    _append->setLayout( _layout );
    // step 4: 设置日志对象
    Logger _logger = Logger::getInstance(LOG4CPLUS_TEXT("test"));
    // step 5: 绑定日志对象和console
    _logger.addAppender(_append);
    // step 6: 设置日志优先级
    _logger.setLogLevel(ALL_LOG_LEVEL);

    LOG4CPLUS_TRACE( logger, "trace and get the fingerprint: " << "random integer: " << std::rand( ) );

    */

}
```

    输出：

```cpp
2019-03-05 23:27:49:841.235|FATAL|19336|e:\vs_test\threads\threads2
\main.cpp:18|DeleteService failed,errCode=[1]| 
2019-03-05 23:27:49:852.238|DEBUG|19336|e:\vs_test\threads\threads2
\main.cpp:19|Service is removed|
```
