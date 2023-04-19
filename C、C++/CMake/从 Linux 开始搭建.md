# 1、环境搭建
## 1.1 编译器 GCC，调试器 GDB
```shell
sudo apt update
sudo apt install build-essential gdb

# 确定版本号
gcc --version
g++ --version
gdb --version
```
## 1.2 CMake
```shell
sudo apt install cmake

cmake --version
```
# 2、GCC 编译
## 2.1 编译过程
### 预处理 *.i
```shell
# -E 仅对输入文件进行预处理
g++  -E test.cpp  -o test.i
```
### 编译 *.s
```shell
# -S 产生汇编语言文件后停止编译
g++  -S test.i  -o   test.s
```
### 汇编 *.o
```shell
# -c 仅把源代码编译为机器语言的目标代码
g++  -c test.s  -o test.o
```
### 链接 bin
```shell
# -o 产生指定文件名的可执行文件
g++ test.o  -o test
```
## 2.2 g++ 参数
### -g

- 编译**带调试信息**的可执行文件；
- 产生能被 GNU 调试器GDB使用的调试信息，以调试程序。
### -O[n]

- 优化，例如省略从未使用过的变量、直接将常量表达式用结果值代替等等；
- -O 对源代码进行基本优化，如-O2，-O3，-On（n 常为0–3） ：
   - **-O** 减小代码的长度和执行时间，等价 -O1；
   - **-O0** 不做优化；
   - **-O1** 默认优化；
   - **-O2** 完成-O1优化之外，还进行一些额外的调整工作，如指令调整等；
   - **-O3** 包括循环展开和其他一些与处理特性相关的优化工作。 
### -l、-L

- **-l** : 指定库文件，如 -lglog ；
- **-L** : 指定库文件路径，如 -L/home/lib 。
### -I

- 指定**头文件**搜索路径。
### -Wall、-w

- **-Wall** ：**打印警告**信息；
- **-w** ：**关闭警告**信息。
### -o

- 指定输出文件名，如 -o test 。
### -D

- **定义宏**：-DDEBUG 开启或关闭 DEBUG。
# 3、GDB 调试器
## 3.1 调试命令参数
```bash
$(gdb)help(h) # 查看命令帮助，具体命令查询在gdb中输入help + 命令

$(gdb)run(r) # 重新开始运行文件（run-text：加载文本文件，run-bin：加载二进制文件）
$(gdb)start # 单步执行，运行程序，停在第一行执行语句
$(gdb)next(n)   # 单步调试（逐过程，函数直接执行）
$(gdb)step(s) # 单步调试（逐语句：跳入自定义函数内部执行）
$(gdb)continue(c) # 继续运行
$(gdb)finish # 结束当前函数，返回到函数调用点
$(gdb)quit(q) # 退出gdb

$(gdb)list(l) # 查看原代码（list-n,从第n行开始查看代码。list+ 函数名：查看具体函数）
$(gdb)backtrace(bt) # 查看函数的调用的栈帧和层级关系
$(gdb)info(i) # 查看函数内部局部变量的数值
$(gdb)set # 设置变量的值
$(gdb)display # 追踪查看具体变量值
$(gdb)print(p) # 打印值及地址

$(gdb)undisplay # 取消追踪观察变量
$(gdb)watch # 被设置观察点的变量发生修改时，打印显示
$(gdb)i watch # 显示观察点

$(gdb)info breakpoints # 查看当前设置的所有断点
$(gdb)enable breakpoints # 启用断点
$(gdb)disable breakpoints # 禁用断点
$(gdb)break+num(b) # 在第num行设置断点
$(gdb)delete breakpoints num(d) # 删除第num个断点

$(gdb)x # 查看内存x/20xw 显示20个单元，16进制，4字节每单元
$(gdb)run argv[1] argv[2] # 调试时命令行传参
$(gdb)frame(f) # 切换函数的栈帧
$(gdb)set follow-fork-mode child # Makefile项目管理：选择跟踪父子进程（fork()）
```
# 4、CMake








