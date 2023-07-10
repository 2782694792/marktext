## 1、安装 git

官网：[https://git-scm.com/]()

全程默认安装：

<img src="file:///E:/MarkText/image cache/2022-08-23-09-12-31-image.png" title="" alt="" data-align="center">

## 2、配置

作为代码提交、下拉、注释时的记录。配置信息存在于文件中 .gitconfig 中。

### 2.1 查看环境配置

<img src="file:///E:/MarkText/image cache/2022-08-23-09-14-51-image.png" title="" alt="" data-align="center">

### 2.2 配置用户和邮箱

```cs
$ git config --global user.name "gitBin"

$ git config --global user.email "2782694792@qq.com"
```

<img src="file:///E:/MarkText/image cache/2022-08-23-09-34-04-image.png" title="" alt="" data-align="center">

### 2.3 生成公钥

```cs
$ ssh-keygen -t rsa -C "email"
```

<img src="file:///E:/MarkText/image cache/2022-08-24-10-22-18-image.png" title="" alt="" data-align="center">

<img src="file:///E:/MarkText/image cache/2022-08-23-10-20-24-image.png" title="" alt="" data-align="center">

### 2.4 记住密码

```cs
git config --global credential.helper store
```

<img src="file:///E:/MarkText/image cache/2022-08-23-10-19-26-image.png" title="" alt="" data-align="center">
