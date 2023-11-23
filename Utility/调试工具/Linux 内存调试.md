

## 内存异常

### 2.1 构造与析构

#### malloc / free

- 从**堆**上动态分配内存；

  > 1. 使用 `free` 释放已分配的内存；、
  > 2. 返回 `void *`，需要进行强制转换；
  > 3. 分配失败时，返回 **NULL**（分配后可以进行判断）；
  > 4. 需要**显式地指出**分配的内存大小；

- 可重新分配内存大小；

#### new / delete

- 从**自由存储区**上为对象动态分配内存空间；

> 1. 自由存储区可以是堆，还可以是静态存储区；
> 2. **定位 new** 可不为对象分配内存空间，如 `new (place_address) type`；
> 3. 返回对象类型的指针，属于**类型安全**；
> 4. 分配失败时，会抛出 **bac_alloc** 异常；

- operator new / delete 的实现可以基于 malloc；
- delete [ ] 释放数组分配的内存；

![](../..\image cache\构造与析构.png)

### 2.2 内存泄漏

- 申请内存是否成功（栈申请：先进后出）；
- 堆内存释放是否异常（手动释放，派生类优先，基类 virtual 析构）；
- 相关函数存在内存处理：memset、memcpy 等标准函数处理是否异常丢出；
- 复杂结构体初始化异常：分开成员初始化（避开连续内存的处理操作，避开无法处理的地址）。

## Linux 工具 valgrind

### 3.1 安装

```sh
sudo dnf install valgrind
```

### 3.2 运行

```shell
valgrind --leak-check=full ./my_program
```

### 3.3 异常消息

#### 存储空间不足

- （访问越界、野指针），**写入失败**；
- 写失败容易引起**读失败**；
- **越界**存在**释放非法地址**等；

```shell
==29404==  Address 0x1189AD84 is 0 bytes after a block of size 12 alloc'd

# 进程 ID  说明内容
```

#### 未初始化内存

```shell
==31363== Conditional jump or move depends on uninitialised value(s)
==31363==    at 0x1000041C: main (test1.c:5)
```

#### 内存泄漏

```shell
==31468== Invalid free() / delete / delete[]
==31468==    at 0xFFB9FF0: free (vg_replace_malloc.c:152)
==31468==    by 0x100004B0: main (test2.c:12)
==31468== Address 0x11899258 is 0 bytes inside a block of size 512 free'd
==31468==    at 0xFFB9FF0: free (vg_replace_malloc.c:152)
==31468==    by 0x100004A4: main (test2.c:11)
==31468== 
==31468== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 7 from 1)
==31468== malloc/free: in use at exit: 512 bytes in 1 blocks.									// 一个块 512 字节 仍在使用
==31468== malloc/free: 2 allocs, 2 frees, 1024 bytes allocated.
==31468== For counts of detected errors, rerun with: -v
==31468== searching for pointers to 1 not-freed blocks.												// 一个未释放的块
==31468== checked 167936 bytes.
==31468== 
==31468== LEAK SUMMARY:
==31468==    definitely lost: 512 bytes in 1 blocks.													// 直接丢失
==31468==      possibly lost: 0 bytes in 0 blocks.														// 可能丢失
==31468==    still reachable: 0 bytes in 0 blocks.
==31468==         suppressed: 0 bytes in 0 blocks.
==31468== Use --leak-check=full to see details of leaked memory.
```

#### 非法读写

```shell
==31522== Invalid write of size 4
==31522==    at 0x100004C0: main (test3.c:9)
==31522==  Address 0x11899050 is 0 bytes after a block of size 40 alloc'd
==31522==    at 0xFFB9964: malloc (vg_replace_malloc.c:130)
==31522==    by 0x10000474: main (test10.c:4)
==31522== 
==31522== Invalid read of size 4
==31522==    at 0x1000050C: main (test3.c:12)
==31522==  Address 0x11899050 is 0 bytes after a block of size 40 alloc'd
==31522==    at 0xFFB9964: malloc (vg_replace_malloc.c:130)
==31522==    by 0x10000474: main (test10.c:4)
==31522== 
==31522== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 7 from 1)
==31522== malloc/free: in use at exit: 0 bytes in 0 blocks.
==31522== malloc/free: 2 allocs, 2 frees, 84 bytes allocated.
==31522== For counts of detected errors, rerun with: -v
==31522== No malloc'd blocks -- no leaks are possible.
```

