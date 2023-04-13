# JSON

    **J**ava**S**cript **O**bject **N**otation(JavaScript 对象表示法)

## 定义

- 存储和交换文本信息的语法，类似 XML；

- 比 XML 更小、更快，更易解析，属于轻量级的文本数据交换格式；

- 使用 Javascript语法来描述数据对象，但仍独立于语言和平台；

- 易于人阅读和编写，C/C++、java、php等多种语言都支持。

```json
{
    "sites": [ // 数组
    { "name":"菜鸟教程" , "url":"www.runoob.com" }, // 对象（站点记录）
    { "name":"google" , "url":"www.google.com" }, 
    { "name":"微博" , "url":"www.weibo.com" }
    ]
}
```

## 与 XML 的区别

### 相同之处

- 都是纯文本，具有"自我描述性"（易于解读）；
- 都具有层级结构；
- JSON 可通过 JavaScript 进行解析；
- JSON 数据可使用 AJAX 进行传输。

### 不同之处

- JSON 不需要结束标签，还更加简短；
- JSON 可直接使用现有 JavaScript 对象进行解析；
- 针对 AJAX 应用，JSON 读写速度更快更简单；
- JSON 能够使用内建的 JavaScript eval() 方法进行解析；
- JSON 可以使用数组，而且不使用保留字。

> XML 需要使用 XML 解析器解析，JSON 可以使用标准的 JavaScript 函数来解析。
> 
> - JSON.parse : 将一个 JSON 字符串转换为 JavaScript 对象。
> - JSON.stringify(): 将 JavaScript 值转换为 JSON 字符串。

# 语法

    JSON 语法是 JavaScript 对象表示语法的子集。

- 数据在名称/值对中；
- 数据由逗号 , 分隔；
- 使用斜杆 \ 来转义字符；
- 大括号 {} 保存对象；
- 中括号 [] 保存数组，数组可以包含多个对象。

## 文件

- JSON 文件的文件类型是 .json
- JSON 文本的 MIME 类型是 application/json

## 对象

大括号 { } 保存

<img src="file:///D:/Download/MarkText/workspace/image/2022-12-08-20-54-31-image.png" title="" alt="" data-align="center">

## 数组

中括号 [ ] 保存

<img src="file:///D:/Download/MarkText/workspace/image/2022-12-08-20-55-10-image.png" title="" alt="" data-align="center">

## 值

    双引号括起来的字符串（string）、数值(number)、true、false、 null、对象（object）或者数组（array）：

- 数字（整数或浮点数）；
- 字符串（在双引号中）；
- 逻辑值（true 或 false）；
- 数组（在中括号中）；
- 对象（在大括号中）；
- null。

<img src="file:///D:/Download/MarkText/workspace/image/2022-12-08-20-56-38-image.png" title="" alt="" data-align="center">

# Demo 解析

[jsonParse: Json 文件解析 —— 浅拷贝 (gitee.com)](https://gitee.com/zheng_shuobin/jsonParse)
