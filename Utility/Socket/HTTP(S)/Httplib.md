# httplib

- httplib 是一个基于 `C++11` 的轻量级 HTTP 客户端和服务器库；

- 特点：简单易用、跨平台、高性能、支持 HTTPS 和 WebSocket 等协议。

- [cpp-httplib库的原理](https://blog.csdn.net/weixin_43939593/article/details/104263043)；

- [cpp-httplib: https://github.com/yhirose/cpp-httplib.git](https://gitee.com/iqxg/cpp-httplib?_from=gitee_search)

## 原理

1. 使用了 `C++11` 的标准库和 `Boost` 库来实现 HTTP 客户端和服务器的功能；

2. **客户端使用 Boost.Asio** 库来进行网络通信，**服务器使用 C++11 的标准库和 Boost 库**来进行网络通信。

## 优点

1. 简单易用：API 设计简单易用，可以快速上手进行 HTTP 通信；

2. 跨平台：可以在 Windows、Linux、macOS 等多个平台上运行；

3. 高性能：使用了异步 IO 和多线程等技术来提高性能；

4. 支持 HTTPS 和 WebSocket 等协议；

5. 轻量级：代码量比较少，不需要依赖大量的第三方库，可以方便地嵌入到其他项目中。

## 缺点

1. 其功能相对较为简单，不支持一些高级的 HTTP 功能，例如 HTTP/2、HTTP/3 等协议；

2. httplib 的性能虽然比较高，但仍然无法达到一些专业的 HTTP 服务器的性能水平。

## 文件目录

<img src="../../../image%20cache/httplib%20文件目录.png" title="" alt="" data-align="center">

- httplib.h：库的头文件；

- example：代码示例；

- split.py：python 执行文件，执行成功后会在同目录下生成一个 out 文件夹，里面包含了分离的 .cc 和 .h 文件。

# 基本结构

## 发送请求

```cpp
class Request
{
    std::string method; // 请求方法
    std::string path; // 请求路径
    map<std::string, std::string> param;  // 查询字符串(查询字符串中)
    map<std::string, std::string> headers;// 键值对头部
    std::string body; // 正文
};
```

## 响应数据

```cpp
class Response
{
    int status; // 返回的状态码
    map<std::string, std::string> headers; // 返回的键值对头部
    std::string body; //正文
};
```

## 服务端

```cpp
class Server {
public:
  using Handler = std::function<void(const Request &, Response &)>;
  using HandlerWithContentReader = std::function<void(
      const Request &, Response &, const ContentReader &content_reader)>;
  using Expect100ContinueHandler =
      std::function<int(const Request &, Response &)>;

  Server();

  virtual ~Server();

  virtual bool is_valid() const;

  Server &Get(const char *pattern, Handler handler);
  Server &Post(const char *pattern, Handler handler);
  Server &Post(const char *pattern, HandlerWithContentReader handler);
  Server &Put(const char *pattern, Handler handler);
  Server &Put(const char *pattern, HandlerWithContentReader handler);
  Server &Patch(const char *pattern, Handler handler);
  Server &Patch(const char *pattern, HandlerWithContentReader handler);
  Server &Delete(const char *pattern, Handler handler);
  Server &Delete(const char *pattern, HandlerWithContentReader handler);
  Server &Options(const char *pattern, Handler handler);
}
```

示例

```cpp
#define CPPHTTPLIB_OPENSSL_SUPPORT
#include "path/to/httplib.h"
// HTTP
httplib::Server svr;
// HTTPS
httplib::SSLServer svr;
svr.Get("/hi", [](const httplib::Request &, httplib::Response &res) {
  res.set_content("Hello World!", "text/plain");
});
svr.listen("0.0.0.0", 8080);
```

## 客户端

```cpp
class Client
{
    Client(host,port);
    Get()
    Post()
    Put()
    ...
};
```

示例

```cpp
#define CPPHTTPLIB_OPENSSL_SUPPORT
#include "path/to/httplib.h"
// HTTP
httplib::Client cli("http://cpp-httplib-server.yhirose.repl.co");
// HTTPS
httplib::Client cli("https://cpp-httplib-server.yhirose.repl.co");
auto res = cli.Get("/hi");
res->status;
res->body;
```

## SSL Support

SSL support 要用到 CPPHTTPLIB_OPENSSL_SUPPORT. libssl and libcrypto 的支持。 
现在只有 httplib 1.1.1 支持ssl 服务器。

```cpp
#define CPPHTTPLIB_OPENSSL_SUPPORT
#include "path/to/httplib.h"
// Server
httplib::SSLServer svr("./cert.pem", "./key.pem");
// Client
httplib::Client cli("https://localhost:1234"); // scheme + host
httplib::SSLClient cli("localhost:1234"); // host
// Use your CA bundle
cli.set_ca_cert_path("./ca-bundle.crt");
// Disable cert verification
cli.enable_server_certificate_verification(false);
```

# 响应流程

```cpp
#include "cpp-httplib"
void helloworld(Request& req, Response& rsp) {
    // 1. 业务处理
    // 2. 填充 rsp 对象，正文（包括类型）、响应状态等
    rsp.set_content("<html><h1>hello</h1></html>", "text/html");
    rsp.status = 200;
}

int main()
{
    Server srv;  // 创建服务端对象
    srv.Get("/",helloworld);
    srv.listen("0.0.0.0",9000); // 0.0.0.0 本机的任意一块网卡地址， 9000端口
}
```

## Get

- Get ( 请求资源路径path, 回调函数指针 )

- 作用：将资源路径以及请求方法还有回调函数，存储在 server 对象中的 map 表中；

- | 资源  | 方法  | 回调函数地址（指针） |
  | --- | --- | ---------- |
  | "/" | GET | helloworld |

> 1. Get 先注册对应关系，并记录在 map 中；
> 
> 2. 当 listen 开始时，建立服务端；
> 
> 3. 收到 http 请求后进行数据包解析，若在 map 中找到请求 path 对应 Get 接口传入的 path 的一个对应关系，则创建线程回调函数完成对请求的业务处理；

## listen

- 搭建 tcp 服务器，监听本机所有网卡 9000 端口，等待客户端传来数据；

- 接收到数据请求，则创建线程进行请求处理；
  
  > 1. 按照 http 协议格式解析请求数据，并将解析结果放入到一个实例化的 Request 对象中（方法、路径、查询字符串、头部信息、正文等）；
  > 
  > 2. 在 server 对象中的 map 表进行查找对应关系，对应成功则传入回调函数处理请求，处理完毕后填充 response 对象并组织 http 响应返回客户端。

# 应用示例

## 服务端

> 线程监听阻塞

### 数据响应

```cpp
#define SERVER_CERT_FILE "./cert.pem"
#define SERVER_PRIVATE_KEY_FILE "./key.pem"

int main(void) {
#ifdef CPPHTTPLIB_OPENSSL_SUPPORT
  SSLServer svr(SERVER_CERT_FILE, SERVER_PRIVATE_KEY_FILE);
#else
  Server svr;
#endif

  if (!svr.is_valid()) {
    printf("server has an error...\n");
    return -1;
  }

  svr.Get("/hi", [](const Request& req, Response& res) {
    res.set_content("Hello World!", "text/plain");
  });

  svr.Get(R"(/numbers/(\d+))", [&](const Request& req, Response& res) {
    auto numbers = req.matches[1];
    res.set_content(numbers, "text/plain");
  });

  svr.Get("/body-header-param", [](const Request& req, Response& res) {
    if (req.has_header("Content-Length")) {
      auto val = req.get_header_value("Content-Length");
    }
    if (req.has_param("key")) {
      auto val = req.get_param_value("key");
    }
    res.set_content(req.body, "text/plain");
  });

  svr.Get("/stop", [&](const Request& req, Response& res) {
    svr.stop();
  });

  svr.listen("localhost", 1234);

  return 0;
}
```

### 响应 JSON

```cpp
#include <stdio.h>
#include <iostream>
#include <jsoncpp/json/json.h>
#include "httplib.h"

using namespace httplib;
using namespace std;

int g_val = 100;
void Get_CallBackFunc(const Request& req, Response& resp)
{
    printf("%d\n", g_val);
    cout << req.method << endl; 
    printf("i am Get_CallBackFunc\n");

    const char* lp = "<html><h2>hello bite!</h2></html>";
    resp.set_content(lp, strlen(lp), "text/html");
}

int main()
{
    Server http_svr;
    int a = 10;
    http_svr.Get("/abc", Get_CallBackFunc);
    http_svr.Post("/login", [](const Request& req, Response& resp){
            cout << req.body << endl;

            Json::Value resp_json;
            resp_json["login_status"] = true;
            Json::FastWriter w;
            resp.body = w.write(resp_json);
            resp.set_header("Content-Type", "application/json");
            });
    http_svr.set_mount_point("/", "./web");
    http_svr.listen("0.0.0.0", 21010);

    return 0;
}
```

### 提供上传

```cpp
#include "httplib.h"
#include <iostream>
#include <fstream>

using namespace httplib;
using namespace std;

int main(void)
{
    Server svr;
    svr.Post("/post", [](const Request &req, Response &res) {
        auto music_file = req.get_file_value("music_file");

        cout << "image file length: " << music_file.content.length() 
             << endl
             << "image file name: " << music_file.filename << endl;
        {
            ofstream ofs(music_file.filename, ios::binary);
            ofs << music_file.content;
        }
        res.set_content("done", "text/plain");
    });
    svr.set_mount_point("/", "./www");
    /// listen
    svr.listen("0.0.0.0", 9089);
}
```

## 客户端

### 数据请求

```cpp
#include <httplib.h>
#include <iostream>

int main(void)
{
  httplib::Client cli("localhost", 1234);

  if (auto res = cli.Get("/hi")) {
    if (res->status == 200) {
      std::cout << res->body << std::endl;
    }
  } else {
    auto err = res.error();
    ...
  }
}
```

### 请求下载

```cpp
if(auto res = client.Get("/hi", [](uint64_t len, uint64_t total) {
     printf("%lld / %lld bytes => %d%% complete\n",len, total,(int)(len*100/total));
     return true; // return 'false' if you want to cancel the request.
}))
{
    printf("status:%d\n",res->status);
    if(res->status == 200)
    {
        std::ofstream out;
        out.open("down_file.tar", std::ios_base::binary | std::ios::out);
        printf("savefile!\n");
        if(out.is_open())
        {
            out<<res->body;
            out.close();
            printf("down load file finished!\n");
        }
        else
        {
            printf("open file error!\n");
        }
    }
}
else
{
    printf("url connect failed! error = %d\n",(int)res.error());
}
```

### 请求上传

```cpp
#ifdef CPPHTTPLIB_OPENSSL_SUPPORT
        httplib::SSLClient client(ser_ip, ser_port);
        client.enable_server_certificate_verification(true);
        client.set_ca_cert_path("ca-bundle.crt");
#else
        httplib::Client client("127.0.0.1:80");
#endif

        std::string body = "...";

        httplib::MultipartFormData updataData;
        updataData.name = "upfile";
        updataData.content = body;
        updataData.filename = file_name;
        updataData.content_type = content_type;
        httplib::MultipartFormDataItems items;
        items.push_back(updataData);
        if (auto res = client.Post(file_upload_url.c_str(), items))
        {
            if (res->status != 200)
            {
                printf("error status = %d\n",res->status);
                return -4;
            }
            else
            {
                return 0; // upload file success
            }
        }
        else
        {
            printf("url connect failed! error = %d\n",(int)res.error());
            return -5;
        }
    }
    else
    {
        printf("read upload file error\n");
        return -3;
    } 
```
