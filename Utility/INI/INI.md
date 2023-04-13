# Initialization File

    初始化文件，是 windows 系统配置文件所采用的存储格式，统管 windows 的各项配置。

- 文件会以不同的扩展名，如".ini.",".cfg",".conf"等。

- 用来配置应用软件以实现不同的需求；

- 可以用来存放软件信息、注册表信息等。

## 格式

    INI 文件由节/段、键、值组成。

### 节/段

- 段与段的名字不能重复；

- [section]，段内的参数对是有序的，可重复的；

### 参数

    （键 = 值）（name = value）

### 注解

    使用分号（ ; ）表示，分号后面的文字直到该行结尾，全部为注解。

### 示例

```ini
; comment textINI文件的数据格式的例子（配置文件的内容）　
[Section1 Name]
KeyName1=value1
KeyName2=value2

[Section2 Name]
KeyName21=value21
KeyName22=value22
```

> [Section1 Name] ：用来表示一个段落，以此来区分不同用途的参数区；
> 
> KeyName1 = value1 ：用来表示一个参数名和值。
