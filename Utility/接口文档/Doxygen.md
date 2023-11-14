# Doxygen

## 一、介绍

Doxygen 是一个程序的文件产生工具，按照其制定的规则编写程序批注，可将程序中的特定批注转换成为说明文件。

- 使用：一是特定格式的批注撰写，二是利用Doxygen的工具来产生文档；
- 生成的文档格式有 [Doxygen Manual: Output Formats](https://www.doxygen.nl/manual/output.html)：HTML、XML、LaTeX、RTF、Unix Man Page 等，其中 HTML 可以打包成 CHM 格式，LaTeX 可以透过一些工具产生 PS 或 PDF 文档。

安装：

1. [Doxygen: Downloads](https://www.doxygen.nl/download.html)；
2. [Graphviz](https://www.graphviz.org/download/): 用于绘制DOT语言脚本描述的图形。Doxygen 使用 graphviz 自动生成类之间和文件之间的调用关系图；
3. [Windows Help Workshop 1.32](https://www.helpandmanual.com/download/htmlhelp.exe)：Doxygen 使用，生成 CHM 格式的文档。

文件命令的使用：[Doxygen Manual: Documenting the code](https://www.doxygen.nl/manual/docblocks.html)；

## 二、配置

- 配置文件：[百度网盘](https://pan.baidu.com/s/10zAAM1PyTCn7dDJxCpTlEQ?pwd=c3kt)；

### 2.1 wizard 标签

<img src="E:\marktext\image cache\Doxygen_wizard_project.png" style="zoom:80%;" />

<img src="E:\marktext\image cache\Doxygen_wizard_mode.png" style="zoom:80%;" />

<img src="E:\marktext\image cache\Doxygen_wizard_output.png" style="zoom:80%;" />

<img src="E:\marktext\image cache\Doxygen_wizard_Diagrams.png" style="zoom:80%;" />

### 2.2 expert 标签

project：

- **DOXYFILE_ENCODING**：默认 UTF-8 编码格式，显示中文选择 GB2312；
- **TAB_SIZE**：文件中代码的缩进尺寸，如 @code 和 @endocde 段中代码的排版，默认 4；
- **OPTIMIZE_OUTPUT_FOR C**：针对纯 C 代码，生成符合 C 习惯的描述性名称；
- **SUBGROUPING**：输出按类型分组；
- **JAVADOC_AUTOBRIEF**：JavaDoc 注释风格，自动把第一个句号 "." 前的文本作为简要注释；

```cpp
/**
* @brief 简要注释 Brief Description.
*
* 详细注释 Detailed Description
*/
```

Build：

- **EXTRACT_ALL**：输出所有函数，不包含 private、static 函数；
- **EXTRACT_PRIVATE、EXTRACT_STATIC**：输出 private、static 函数；
- **HIDE_UNDOC_MEMBERS**：不显示没有使用 doxygen 格式描述的文档，若启动 EXTRACT_ALL 则被忽略；
- **INTERNAL_DOCS**：输出注解中的 @internal 部分，若未启动则所有 @internal 部分将不可见；
- **CASE_SENSE_NAMES**：启动则所有名称使用小写（不建议）；
- **HIDE_SCOPE_NAMES**：域隐藏（不建议）；
- **SHOW_INCLUDE_FILES**：显示包含文件，启动则在帮助文件中专门``生成一个页面``来描述所有包含文件的列表；
- **INLINE_INFO**：启动则在 inline 函数面前都会有一个 inline 修饰词标明；
- **SORT_MEMBER_DOCS**：排序函数名称，否则按照注释顺序显示；
- **GENERATE_TODOLIST**：`生成 TODOLIST 页面`，其中包含所有 @todo 注解中的内容；
- **SHOW_USED_FILES**：显示函数或类的来源文件（不建议）；
- **SHOW_FILES**：显示文件列表页面；

Input：

- **INPUT_ENCODING**：输入源文件的编码格式，默认 UTF-8；
- **FILE_PATTERNS**：输入文件类型；
- **IMAGE_PATH**：（引用）图片路径；

HTML：

- **CHM_FILE**：CHM 文件名（xx.chm）；
- **HHC_LOCATION**：hhc.exe 文件位置，若在 wizard 标签页中 output 标题里选择了 prepare for compressed HTML (chm) 则需要写入 windows help workshop 程序位置；
- **CHM_INDEX_ENCODING**：建议 GB2312，避免生成 CHM 文件时左边树目录中文乱码；
- **GENERATE_CHI**：索引文件单独输出（不建议）；
- **TOC_EXPAND**：在索引中列举成员名称以及分组（譬如函数，枚举）名称；

### 2.3 Run 标签

- 从源代码中抓取符合规范的注释生成你定制的格式的文档。

## 三、语法

命令大全：[Doxygen Manual: Special Commands](https://www.doxygen.nl/manual/commands.html#cmdmainpage)

### 3.1 注释命令

| 命令        | 字段名                                                       | 语法                                                      |
| ----------- | ------------------------------------------------------------ | --------------------------------------------------------- |
| @file       | 文件名                                                       | file [< name >]                                           |
| @brief      | 简介                                                         | brief { brief description }                               |
| @author     | 作者                                                         | author { list of authors }                                |
| @mainpage   | 主页信息                                                     | mainpage [(title)]                                        |
| @date       | 年-月-日                                                     | date { date description }                                 |
| @author     | 版本号                                                       | version { version number }                                |
| @copyright  | 版权                                                         | copyright { copyright description }                       |
| @param      | 参数，标记变量 [in] [out] 表示输入输出方向                   | param [(dir)] < parameter-name> { parameter description } |
| @return     | 返回                                                         | return { description of the return value }                |
| @retval     | 返回值                                                       | retval { description }                                    |
| @bug        | 漏洞缺陷，链接到所有缺陷汇总的缺陷列表                       | bug { bug description }                                   |
| @details    | 详细描述                                                     | details { detailed description }                          |
| @pre        | 前提条件                                                     | pre { description of the precondition }                   |
| @post       | 用来说明代码项之后的使用条件                                 | post { description of the postcondition }                 |
| @see        | 一段包含其他部分引用的注释，中间包含对其他代码项的名称，自动产生对其的引用链接 | see { references }                                        |
| @link       | 连接(与 @see 类似，{@link [http://www.google.com](https://link.zhihu.com/?target=http%3A//www.google.com)}) | link < link-object>                                       |
| @throw      | 异常描述                                                     | throw < exception-object> { exception description }       |
| @todo       | 对将要做的事情进行注释，链接到所有TODO 汇总的TODO 列表       | todo { paragraph describing what is to be done }          |
| @warning    | 警告信息                                                     | warning { warning message }                               |
| @deprecated | 弃用说明。可用于描述替代方案，预期寿命等                     | deprecated { description }                                |
| @example    | 弃用说明。可用于描述替代方案，预期寿命等                     | deprecated { description }                                |
| @code       | 在注释中开始说明一段代码，直到 @endcode 命令                 | @code .. @endcode                                         |
| @endcode    | 注释中代码段的结束                                           |                                                           |
| @fn         | 函数说明                                                     |                                                           |
| @defgroup   | 定义一个组                                                   | <唯一标识名>                                              |
| @addtogroup | 添加到一个组                                                 |                                                           |
| @ingroup    | 加入到一个组                                                 |                                                           |

```cpp
@exception <exception-object> {exception description} 对一个异常对象进行注释。

@relates <name> 通常用做把非成员函数的注释文档包含在类的说明文档中。

@since {text} 通常用来说明从什么版本、时间写此部分代码。

@note 开始一个段落，用来描述一些注意事项，注解

@par 开始一个段落，段落名称描述由你自己指定

@include 包含文件

@var、@enum、@struct、@class 对变量、枚举、结构体、类进行标注，用于文档生成链接

@file 文件头注释，指定文件，@file [file‐name]
    
@class 类注释，指定类，@class <class‐name> [header‐file] [<header‐name]
    
@remarks 备注说明
    
@sa 参考资料
    
@arg 列表说明参数信息=
```

### 3.2 注释符号

- **[ ]**：表示可选，*[header‐file]*；
- **{ }**：表示重复 0 到 n 次；
- **< >**：表示必须参数，*<class‐name>*
