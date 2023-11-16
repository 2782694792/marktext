# make

- **批处理**工具，本身并没有编译和链接的功能，通过调用 **makefile 文件**中指定的命令来进行编译和链接的；
- 用命令行参数指定为其他文件名：

```shell
$ make -f rules.txt
# 或者
$ make --file=rules.txt
```



## Makefile 文件格式

### 1.1 概述

- 由一系列规则组成，如下：

```shell
<target> : <prerequisites>		# 目标 : 前置条件
[tab]  <commands>							# tab 另起行    命令

# 前置条件与命令为可选，但必须存在一个
```



### 1.2 目标

- 一个 target 构成一个规则；



#### 伪目标

- 目标还可以是某个操作名；

```shell
# 伪目标，删除相关文件
clean:
	rm *.o

make clean
```

- 若存在文件名称与伪目标名字相同时，不会执行指定的命令；

- 声明伪目标：

```shell
.PHONY: clean
clean:
        rm *.o

make
# 命令运行时没有指定目标，则默认执行 makefile 中的第一个目标
```



- 规则依赖执行顺序：

```shell
# 示例 ================================
.PHONY: all foo bar clean

ifeq ($(REORDER),yes)
all: foo bar | order1 order0
else
all: foo bar | order0 order1
endif
	@echo "prerequisites for $@: $^"
  
foo bar :
	@echo "try to make $@"

order0 order1:
	@echo "update $@..." ; touch $@

clean:
	rm -rf order0 order1

# 执行结果 ========================================================

# touch {order0,order1} ; make
try to make foo
try to make bar
update order0...
update order1...
prerequisites for all: foo bar

# rm -rf order0 order1 ; make
try to make foo
try to make bar
update order0...
update order1...
prerequisites for all: foo bar

# rm -rf order0 order1 ; make REORDER=yes
try to make foo
try to make bar
update order1...
update order0...
prerequisites for all: foo bar
```



### 1.3 前置条件

- 通常是**一组文件名**，之间用**空格分隔**；
- 指定目标是否需要进行重新构建：只要有一个前置条件不存在或有过更新，目标就需要重新构建；



```shell
result.txt: source.txt
    cp source.txt result.txt

## 若 source.txt 存在则正常进行，若不存在则需要重新写对应规则

source.txt:
    echo "this is the source" > source.txt

# 执行 =================================
$ make result.txt
$ make result.txt

# 示例：生成多个文件 =============================
source: file1 file2 file3

$ make source
```



### 1.4 命令

- 表示如何更新“目标”文件；
- 由一行（多行）Shell 命令组成，是构建“目标”的具体命令；
- 每行命令前默认有一个 **tab 间隔**，也可以使用内置变量 **.RECIPEPREFIX** 声明。

```shell
.RECIPEPREFIX = >

all:
> echo Hello, world
```



- 每行命令都在一个**单独的 shell 中执行**，属于分开互不影响的；

```shell
var-lost:
    export foo=bar
    echo "foo=[$$foo]"   # 读取不到 foo 的值
```



- 声明内置命令 **.ONESHELL:** 为一个 shell 中运行或命令**写在同一行**上，使得相互关联：

```shell
# 分号分隔 						========================
var-kept:
    export foo=bar; echo "foo=[$$foo]"

# 换行符前加反斜杠转义	========================
var-kept:
    export foo=bar; \
    echo "foo=[$$foo]"

# 内置命令 .ONESHELL		========================
.ONESHELL:

var-kept:
    export foo=bar; 
    echo "foo=[$$foo]"    
```



## Makefile 文件语法

### 2.1 注释

```shell
# 注释
```



### 2.2 回声

- 正常条件下，make 在**执行之前，会打印每条命令；**
- **关闭回声：命令前加上 '@' 符号**；

```shell
test:
    # 这是测试

test:
    @# 这是测试2
    @echo TODO
```



### 2.3 模式匹配符

- 通配符：类似正则运算匹配 '**%**'；
- 符号 ***** 是应用在系统中的，**%** 是应用在 Makefile 文件中的；

```shell
%.o: %.c

f1.o: f1.c	# % := f1
f2.o: f2.c	# % := f2
```



### 2.4 变量与赋值符

- 内置变量：[Special Targets (GNU make)](https://www.gnu.org/software/make/manual/html_node/Special-Targets.html#Special-Targets)

#### 赋值

```shell
VARIABLE = value
# 执行时扩展，允许递归扩展

VARIABLE := value
# 定义时扩展

VARIABLE ?= value
# 只有在变量为空时才设置值

VARIABLE += value
# 将值追加到变量
```



#### 自动变量

- [Automatic Variables (GNU make)](https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html)
- **$@** **目标名**

```shell
a.txt b.txt: 
    touch $@				# touch {a.txt, b.txt}
```



- **$<** **第一个**前置条件

```shell
dest/%.txt: src/%.txt
    @[ -d dest ] || mkdir dest			# 判断 dest 存不存在，不存在则新建
    cp $< $@												# $< (src/%.txt)， $@ (dest/%.txt）
```



- **$?** 指代比目标**更新**的所有前置条件，之间以空格分隔；
- **$^** 指代**所有**前置条件，之间以空格分隔；
- **$\*** 指代匹配符 **% 匹配的部分；**



- **$(@D)** 和 **$(@F)** 分别**指向 $@ 的目录名和文件名**

```shell
$@				# src/input.c
$(@D)			# src
$(@F)			# input.c。
```



- **$(<D)** 和 **$(<F)** 分别指向 **$<** 的**目录名和文件名**；



### 2.5 判断和循环

- 条件函数：[Conditional Functions (GNU make)](https://www.gnu.org/software/make/manual/html_node/Conditional-Functions.html)

```shell
# 判断 ================================

ifeq ($(CC),gcc)
  libs=$(libs_for_gcc)
else
  libs=$(normal_libs)
endif

# 循环 ================================

LIST = one two three
all:
    for i in $(LIST); do \
        echo $$i; \
    done
```



### 2.6 函数

- **shell 函数**用于指定 shell 命令：

```shell
srcfiles := $(shell echo src/{00..99}.txt)
```



- **subst** 用于文本**替换**：
- $(subst from, to, text)

```shell
$(subst .c,.o,test1.c test2.c)

# test1.o test2.o
```



- **wildcard 搜索**符（系统中搜索），用于 bash 的通配符：	

```shell
$(wildcard *.c)

# test1.c test2.c test3.c
```



- **basename** **删减**后缀符：

```shell
$(basename test1.c)

# test1
```



- **patsubst** 函数用于**模式匹配的替换**：
- $(patsubst pattern, replacement, text)

```bash
$(patsubst %.c,%.o,x.c.c bar.c)

# x.c.o bar.o
```



- 后缀名替换：

```bash
min: $(OUTPUT:.js=.min.js)

# 将变量OUTPUT中的后缀名 .js 全部替换成 .min.js 
```



### 示例

```bash
edit : main.o kbd.o command.o display.o 
    cc -o edit main.o kbd.o command.o display.o

main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h
    cc -c display.c

clean :
     rm edit main.o kbd.o command.o display.o

.PHONY: edit clean
```



## make 命令

- make：进行源代码编译，以及功能提供：由 makefile 指定；

```cpp
make all：编译程序、库、文档等（等同于 make）

make install：安装已经编译好的程序。复制文件树中到文件到指定的位置

make unistall：卸载已经安装的程序。

make clean：删除由 make 命令产生的文件

make distclean：删除由 ./configure 产生的文件

make check：测试刚刚编译的软件（某些程序可能不支持）

make installcheck：检查安装的库和程序（某些程序可能不支持）

make dist：重新打包成 packname-version.tar.gz
```