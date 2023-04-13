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
















