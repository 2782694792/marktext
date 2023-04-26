# C++ 与 ftp

[C++的简单FTP客户端实现（一）FTP基础知识_c++ ftp-CSDN博客](https://blog.csdn.net/xuruilll/article/details/122763005)

[C++的简单FTP客户端实现（二）编程_c++ftp_xuruilll的博客-CSDN博客](https://blog.csdn.net/xuruilll/article/details/122763022)

- Websocket 实现

[C/C++实现FTP客户端，实现上传、下载、删除功能_zao_yu的博客-CSDN博客](https://blog.csdn.net/zao_yu/article/details/121875460)

- ftplib

https://gitcode.net/mirrors/mkulke/ftplibpp

## 1.1 简介

- **文件传输协议**（File Transfer Protocol），用于网络上进行文件传输的一套标准协议，工作在 OSI 模型的第七层，TCP 模型的第四层，即**应用层**，**使用 TCP 传输**；

- 允许用户以文件操作的方式（增删改查、传送等）与另一主机相互通信；

- 用 FTP 程序访问远程资源，实现用户往返传输文件、目录管理以及访问电子邮件等，即使双方配有不同操作系统和文件存储方式。

## 1.2 工作原理

### 1.2.1 独特优势

在两台通信的主机之间使用了**两条 TCP 连接**，一条是**数据连接**，用于数据传送，是全双工的，允许同时进行双向数据传输；另一条是**控制连接**，用于传送控制信息（内部命令和命令的响应等控制信息）。

### 1.2.2 基本模型

 ![](https://img-blog.csdnimg.cn/f59f1970e0bf431598e100061ae1afaf.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

> 客户端有三个构件：用户接口、客户端控制进程、客户端数据传送进程；
> 
> 服务器有两个构件：服务器控制进程、服务器数据传送进程；
> 
> 整个交互的 FTP 会话中，控制连接始终是处于**连接状态**的，数据连接则在文件每一次打开后关闭。

## 1.3 用户与传输

### 1.3.1 用户分类

1. Real 账户：FTP 服务上拥有的账号，默认主目录就是账号命名的目录；

2. Guest 用户：特定用户所设置的账户，只能访问自己的主目录；

3. Anonymous 用户：匿名访问，没有指定账户，但可以访问某些公共资源。

### 1.3.2 传输方式

- `ASCII 传输方式`

    当用户正在拷贝的文件包含简单的 ASCII 码文本，且远程服务器上运行的不是 UNIX，则文件传输时通常会自动调整文件内容以便于解析成其他计算机存储文件的格式。

- `二进制传输方式`

    保存文件的位序，以便原始和拷贝的是逐位一一对应的（ASCII 方式一般假设每一字符的第一有效位无意义，而二进制文件传输时所有位都是重要的）。

### 1.3.3 传输模式

FTP 支持两种模式：Standard（PORT，主动方式）、Passive（PASV，被动方式）。

- **主动模式**
  
  - 客户端和服务端的 TCP 21 端口建立连接，用来发送命令，客户端需要接收数据的时候在通道上发送 PORT 命令；
  
  - 传送数据的时候，服务端通过自身的 TCP 20 端口连接客户端指定端口发送数据。

![](https://img-blog.csdnimg.cn/66b9cae70474417a97d0abbdc79de918.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

- **被动模式**
  
  - 建立控制通道和 Standard 模式类似，但建立连接后发送 PASV 命令；
  
  - 服务端收到 PASV 命令后，打开一个临时端口（1023~65535）并通知客户端在该端口上传送数据。

![](https://img-blog.csdnimg.cn/2497ae2011c540c7802521061efd6185.png)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")​

- 使用
1. PORT：服务端到客户端传送数据；PASV：客户端到服务端传送数据；

2. 客户端在防火墙后或内网，PORT 开放端口，服务端很难透过防火墙进行连接的，PASV 模式等待服务器开放端口后进行连接即可；

> 两种模式的登录过程，都是客户端去连接服务端。

## 1.4 控制命令

**语法**：

```bash
ftp [-d] [-g] [-i] [-n] [-v] [-f] [-k realm] [-q[-C]][HostName [Port]]
```

- 登录：

```bash
ftp 用户名@ip:端口号 
# 输入密码
```

- 目录：

```bash
# 查看目录
ls
dir
# 进入目录
cd 目录
# 进入本地目录
lcd 目录
# 查看本地目录
ls
# 上传文件
put [名称] [目录：所有文件]
mput
# 上传文件夹
put -r [目录：所有文件和文件夹]
# 新建
mkdir
# 修改
rename prename newname
# 删除
rm
# 下载
get [文件名称] [-r 文件夹名称]
mget
```

![](https://cdn.nlark.com/yuque/0/2023/png/35714306/1682262230070-4926accf-05e7-4e01-a7ce-8067c7c72d10.png)

# 实现方式

三种实现 Qt 项目中的 FTP 传输功能：

1. Qt4 QFtp

2. Qt5 QNetworkAccessManager

3. POCO FTPClientSession

## 2.1 QFtp

    QFtp 是 Qt4 中专门负责 FTP 传输的类，包括创建目录、删除目录、删除文件、获取文件列表、上传、下载等等常规操作，接口如下。

```cpp
int setProxy(const QString &host, quint16 port);

int connectToHost(const QString &host, quint16 port=21);

int login(const QString &user = QString()          , const QString &password = QString());

int close();

int setTransferMode(TransferMode mode);

int list(const QString &dir = QString());
int cd(const QString &dir);

int get(const QString &file, QIODevice *dev = 0
        , TransferType type = Binary);

int put(const QByteArray &data, const QString &file        , TransferType type = Binary);
int put(QIODevice *dev, const QString &file, TransferType type = Binary);

int remove(const QString &file);

int mkdir(const QString &dir);
int rmdir(const QString &dir);

int rename(const QString &oldname, const QString &newname);
```

## 2.2 QNetworkAccessManager

    QNetworkAccessManager 是 Qt 专门负责网络请求的模块，包含了 http 的 post、get、put，put 用来 Qt5 实现 FTP 上传功能，get 用来实现下载功能，仅仅只能上传下载，接口如下。

```cpp
*put(const QNetworkRequest &request, QIODevice *data);
QNetworkReply *put(const QNetworkRequest &request                    , const QByteArray &data);
QNetworkReply *put(const QNetworkRequest &request                    , QHttpMultiPart *multiPart);

QNetworkReply *get(const QNetworkRequest &request);
```

## 2.3 FTPClientSession

    FTPClientSession 是 POCO 中网络模块里负责实现 FTP 的类，接口如下，[POCO](https://blog.csdn.net/llg070401046/article/details/52382481 "POCO") 是目前最流行的 C++ 轻量级封装库之一，里面包含了很多常用的封装库。

```cpp
void setTimeout(const Poco::Timespan& timeout);
Poco::Timespan getTimeout() const;

void setPassive(bool flag, bool useRFC1738 = true);
bool getPassive() const;

virtual void open(const std::string& host, Poco::UInt16 port                , const std::string& username = ""
                , const std::string& password = "");
virtual void login(const std::string& username                    , const std::string& password);
void logout();
void close();

std::string systemType();

void setFileType(FileType type);
FileType getFileType() const;

void setWorkingDirectory(const std::string& path);
std::string getWorkingDirectory();

void cdup();
void rename(const std::string& oldName, const std::string& newName);
void remove(const std::string& path);
void createDirectory(const std::string& path);
void removeDirectory(const std::string& path);

std::istream& beginDownload(const std::string& path);
void endDownload();
std::ostream& beginUpload(const std::string& path);
void endUpload();

std::istream& beginList(const std::string& path = ""
                        , bool extended = false);
void endList();

void abort();

int sendCommand(const std::string& command, std::string& response);
int sendCommand(const std::string& command                , const std::string& arg, std::string& response);

bool isOpen() const;
bool isLoggedIn() const;
bool isSecure() const;

const std::string& welcomeMessage();
```

## 2.4 区别

- QFtp：

优点：接口完整；

缺点：源码编译，需要解决编码问题，异常处理不友好（断网，异常信号，连接状态不会改变等）；

- QNetworkAccessManager

优点：简单，集成度高；

缺点：不可文件操作，通过二进制方式上传，且不可改变上传方式；

- FTPClientSession

优点：使用简单，可异常捕捉；

缺点：缺少上传下载的进度接口或回调，可通过流的方式获取文件列表，但文件名带有空格时读取内容异常。

# QFtp

实现 QFtp 的传输，可以通过**源码文件**进行项目编写，也可以通过编译的方式进行，以下通过编译进行举例。

## 3.1 下载编译

1. 下载：[qt/qtftp (github.com)](https://github.com/qt/qtftp "qt/qtftp (github.com)")

2. 项目 .pro 文件：”qtftp-master\src\qftp\qftp.pro“

```cpp
// 源文件
#CONFIG += static
#CONFIG -= shared
// 静态库文件 .a
CONFIG += staticlib 
// 动态库文件 .dll
CONFIG += shared
```

1. 头 .h 文件： “qtftp-master\src\qftp\qftp.h”

```
//#include <QtFtp/qurlinfo.h>
#include <qurlinfo.h>
```

1. 进行编译

![](https://img-blog.csdnimg.cn/560f946df4e54a4b989f9bbb0c458935.png)

## 3.2 部署依赖

- 动态库文件 .dll，拷贝至 “E:\Qt\V5.9.3\5.9.3\mingw53_32\bin” 下：

![](https://img-blog.csdnimg.cn/033f6ce43bb949a6ad3917d6c0e55625.png)

- 静态库文件 .a，拷贝至 “E:\Qt\V5.9.3\5.9.3\mingw53_32\lib” 下:

![](https://img-blog.csdnimg.cn/0c408c557e5d4674b5d2abc3afbe0670.png)

- 头文件拷贝至 “E:\Qt\V5.9.3\5.9.3\mingw53_32\include\QtNetwork” 下，同时添加无后缀名文件 QFtp:

![](https://img-blog.csdnimg.cn/3a0d5798494f4089bb68f6bb533ea4dc.png)

> QFtp，文本打开，保存：#include "qftp.h"

- 使用编译库：

```
// 动态库添加
// 项目 —— 右键 —— 添加库 —— 外部库，动态
win32:CONFIG(release, debug|release): LIBS += 
        -L$$PWD/../../../Qt/V5.9.3/5.9.3/mingw53_32/lib/ -lQt5Ftp
else:win32:CONFIG(debug, debug|release): LIBS +=
        -L$$PWD/../../../Qt/V5.9.3/5.9.3/mingw53_32/lib/ -lQt5Ftpd

INCLUDEPATH += $$PWD/../../../Qt/V5.9.3/5.9.3/mingw53_32/include
DEPENDPATH += $$PWD/../../../Qt/V5.9.3/5.9.3/mingw53_32/include
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

## 3.3 应用示例

demo：[Qt-Qftp: Qftp 实现 ftp 上传，文件以及文件夹结构，处理定时上传 (gitee.com)](https://gitee.com/zheng_shuobin/qt-qftp "Qt-Qftp: Qftp 实现 ftp 上传，文件以及文件夹结构，处理定时上传 (gitee.com)")

    [参考链接](https://blog.csdn.net/jiesunliu3215/article/details/111469455 "参考链接")，所有的 QFtp 传输操作，都会涉及到两个信号的发出：

```cpp
void commandStarted(int);
void commandFinished(int, bool);

    connect(&ftp,&QFtp::commandFinished,this,[&](){
        if(ftp.currentCommand() == QFtp::List){
            qDebug() << "commandFinished. QFtp::List " << fileList ;
            ui->listWidget->addItems(fileList);
            if(m_loop.isRunning())
                m_loop.exit(); // FTP 属于异步操作，事件循环做同步操作
        }
    });
```

- 连接与登录

```cpp
/// 1. 连接服务端
    QString host = "127.0.0.1";
    quint16 port = 21;
    if (ftp->state() != QFtp::LoggedIn)
    {
        ftp->connectToHost(host, port);
        qDebug() << QString("connectToHost : ") << QString::number(
                        ftp->connectToHost(host, port));

        /// 2. 登录账户

        ftp->login(username, pwd);
    }
```

- 设置模式并传输

```cpp
/// 3. 设置传输模式: 被动模式
        ftp->setTransferMode(QFtp::Passive);

    /// 4. 操作
    ftp->mkdir(GbkAndUtf8("1as2to3文件夹4_5")); // 新建文件夹，中文处理

    QFile file("F:\\server\\123.txt");
    if(file.open(QIODevice::ReadOnly)){
        ftp->put(&file, QFileInfo(file).fileName()); // 文件上传
    }

    QString filelist = "/";
    ftp->list(filelist); // 刷新指定目录
```

- 读取文件列表

```cpp
void MainWnd::addToList(const QUrlInfo url)
{
    QString
        fileSize; // 用于存储文件大小，根据文件大小字节，设置文件在树列表的单位
    if (urlInfo.size() >= 0 && urlInfo.size() < 1024) {
        fileSize = QString::number(urlInfo.size()) + "Byte";
    }
    else if (urlInfo.size() >= 1024 && urlInfo.size() < 1024 * 1024) {
        fileSize = QString::number(urlInfo.size() / 1024.0, 'f', 2) + "KB";
    }
    else if (urlInfo.size() >= 1024 * 1024 &&
             urlInfo.size() < 1024 * 1024 * 1024) {
        fileSize =
            QString::number(urlInfo.size() / 1024 / 1024.0, 'f', 2) + "MB";
    }
    else if (urlInfo.size() >= 1024 * 1024 * 1024) {
        fileSize =
            QString::number(urlInfo.size() / 1024 / 1024 / 1024.0, 'f', 2) +
            "GB";
    }

    QTreeWidgetItem* item = new QTreeWidgetItem;

    item->setText(0, fromSpecialEncoding(urlInfo.name()));
    item->setText(1, fileSize);
    item->setText(2, urlInfo.lastModified().toString("yyyy/MM/dd hh:mm"));
    item->setText(3, urlInfo.owner());
    item->setText(4, urlInfo.group());


    QPixmap pixmap(urlInfo.isDir() ? ":/img/img/dir.png" :                                     ":/img/img/file.png");
    item->setIcon(0, pixmap);
    isDirectory[urlInfo.name()] = urlInfo.isDir();
    ui.treeWidget->addTopLevelItem(item);
    if (!ui.treeWidget->currentItem()) {
        ui.treeWidget->setCurrentItem(ui.treeWidget->topLevelItem(0));
        ui.treeWidget->setEnabled(true);
    }
}
```

> 目录列表刷新信号：
> 
> connect(&d->pi.dtp, SIGNAL(listInfo(QUrlInfo))         ,SIGNAL(listInfo(QUrlInfo)));
> 
> 自定义槽函数实现：
> 
> connect(ftp, SIGNAL(listInfo(const QUrlInfo &)),this         , SLOT(addToList(const QUrlInfo)));
> 
> // QUrlInfo 包含文件、文件夹的各个信息，以此实现刷新读取文件列表

- 传输进度条

```cpp
connect(&ftp,&QFtp::dataTransferProgress,this,[=,&fileInfo,&ftp](
        qint64 readBytes, qint64 totalBytes)
{ 
    ui->progressBar->setValue(static_cast<int>(readBytes/totalBytes));

    if(readBytes/totalBytes == 1){
        if(file->isOpen()){
            file->close();
        }
        ftp.disconnect();
        ftp.close();
    }
});
```

- 编码格式转换

```cpp
QString FtpUpload::fromSpecialEncoding(const QString& inputStr)
{
    if (ui.CB_EncodingFormat->currentIndex() == 0) {
        QTextCodec* codec = QTextCodec::codecForName("gbk");
        return codec->toUnicode(inputStr.toLatin1());
    }
    else {
        QTextCodec* codec = QTextCodec::codecForName("utf8");
        return codec->toUnicode(inputStr.toLatin1());
    }
}

QString FtpUpload::toSpecialEncoding(const QString& inputStr)
{
    if (ui.CB_EncodingFormat->currentIndex() == 0) {
        QTextCodec* codec = QTextCodec::codecForName("gbk");
        return QString::fromLatin1(codec->fromUnicode(inputStr));
    }
    else {
        QTextCodec* codec = QTextCodec::codecForName("utf8");
        return QString::fromLatin1(codec->fromUnicode(inputStr));
    }
}
```

- 返回上一级

```cpp
currentPath = currentPath.left(currentPath.lastIndexOf("/"));
ftp.cd(currentPath);
```
