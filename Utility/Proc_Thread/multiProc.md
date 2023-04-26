# 多进程

## 前提

**fork** 是一个在 **Linux** 系统环境下专有的函数，现有进程调用 fork 后将会创建一个新的进程。

1. 进程：包括代码、数据、CPU 分配给进程的资源；

2. fork() 函数通过系统调用**创建一个与原来进程几乎完全相同的进程**，也就是两个进程可以做完全相同的事，但如果初始参数或者传入的变量不同，两个进程也可以做不同的事；

3. 调用 fork() 函数后，**系统先给新的进程分配资源**，例如存储数据和代码的空间，然后把原来的进程的所有值都**复制到新的进程**中，只有少数值与原来的进程的值不同。

## 创建

#### 单个子进程

```php
void main_crtOnce() {
    int   count = 0;
    pid_t pid;
    printf("Start : main create once\n"); // 执行一次，存在 \n 刷新缓冲区
    printf("Start : main create once"); // 执行两次，无 \n 时仅仅保存在缓冲区
    pid = fork(); // one call, two return
    if (pid == -1) {
        perror("fork error");
        exit(1);
    }
    else if (pid == 0) { // 子进程
        count = 11;
        printf("child, count = %d, pid = %u, ppid = %u\n", count, getpid(),
               getppid());
    }
    else { // 父进程
        count = 22;
        printf("parent, count = %d, pid = %u, ppid = %u\n", count, getpid(),
               getppid());
        sleep(1);
    }
    printf("Finish : create once, count = %d!\n", count);
}

// ----------------------------------------
Start : main create once
parent, count = 22, pid = 5129, ppid = 1585
child, count = 11, pid = 5130, ppid = 5129
Finish : create once, count = 11!
Finish : create once, count = 22!
```

1. 父进程先运行，创建子进程，共享资源，同时共同运行（此时进程运行无固定顺序，由系统的进程调用策略决定），fork 仅仅被调用一次，从 fork 创建开始之后，能够返回两次运行结果；

2. fork 有三个返回值：父进程中 fork 返回创建子进程的 pid，子进程中 fork 返回 0，异常情况时返回负数；

3. 先结束子进程，再结束父进程；

4. fork 异常，可能有两种原因：
   
   1. 当前的进程数已经达到了系统**规定的上限**，这时 errno 的值为 **EAGAIN**；
   
   2. 系统**内存不足**，errno 的值为 **ENOMEM**。

5. 进程之间，资源是独立的，非共用，存在不同地址中，通过 pid 来识别父子进程。

#### 多个子进程

```php
void main_crtMore() {
    int   i = 0;
    pid_t pid;
    for (i = 0; i < 5; i++) {
        pid = fork();
        if (pid == -1) {
            perror("fork error");
            exit(1);
        }
        else if (pid == 0) { // 子进程
            break;
        }
    }
    if (i < 5) {
        sleep(i);
        printf("I'm %dth child, pid = %u, ppid = %u\n", i + 1, getpid(),
               getppid());
    }
    else {
        sleep(i);
        printf("I'm parent, pid = %u, ppid = %u\n", getpid(), getppid());
    }
} 

//--------------------------
```

## 进程计算

```php
void main_countMore() {
    int i = 0;
    printf("i son/pa ppid pid fpid\n");
    // ppid指当前进程的父进程pid
    // pid指当前进程的pid,
    // fpid指fork返回给当前进程的值
    for (i = 0; i < 2; i++) {
        pid_t fpid = fork();
        if (fpid == 0)
            printf("%d child %4d %4d %4d\n", i, getppid(), getpid(), fpid);
        else
            printf("%d parent %4d %4d %4d\n", i, getppid(), getpid(), fpid);
    }
} 

// ----------------------------
i son/pa ppid pid fpid
0 parent 1585 8929 8930
0 child 8929 8930    0
1 parent 1585 8929 8931
1 child 8929 8931    0
1 parent    1 8930 8932
1 child    1 8932    0
```

1. i == 0，父进程a 与子进程b 共同运行一次；

2. i == 1，此时已经存在两个进程a b，两个进程都进行 fork，每个进程产生两个进程，总共产生四个进程：父进程 a1  b1，子进程 a2 b2，父进程 a1 b2 均打印；

3. i == 2，此时结束循环，不再 fork，a2 b2 再无子进程出现，直接打印，然后结束。

4. 当前进程执行 fork，则上升为“父进程”；

5. 进程进入结束阶段，由原来的父进程开始，当子进程无父进程时，属于非法，开始置换父进程，进程 ppid 为 1 是永生进程；

6. 循环 fork 开始，不执行则仅仅 1 个进程，执行一次产生 1 个子进程（2 个进程），执行两次产生 3 个子进程（4 个进程），即会有 2 的 N 次幂个进程数量。

#### 示例

```php
#include <stdio.h> 
#include <unistd.h> 
int main(int argc, char* argv[]) 
{ 
  fork(); // 1 2
  fork() && fork() || fork(); // 5 10
  fork(); // 2 20
  return 0; 
}
```

1. && 与运算：对于左算数，为 0 不进行，不为 0 进行运算；

2. || 或运算：对于左算数，为 0 进行，不为 0 则进行运算；

3. fork 产生 2 个进程，其中，（1）一个返回大于 0 进行与运算：再次 fork，产生 2 个进程，其中 1 个返回等于 0，进入非运算：产生 2 个进程；（2）一个返回等于 0 进行非运算，产生 2 个进程，总共有 5 个进程。

4. 2 * 5 * 2 为  20 个进程，包括一个主进程 main。

# exec 函数族

在 Linux 中，有两组不同的 exec 函数族：System V（以下为例）和 POSIX。它们具有相似的函数名，但语法略有不同。

## 说明

- fork 创建的子进程几乎是父进程的副本，当需要子进程去执行另外的程序时，通过 exec 函数族在同一个进程中或不同的程序中执行其他可执行文件；

- exec 函数族可以根据指定文件名或目录名找到可执行文件，并用它来取代原调用进程的数据段、代码段和堆栈段（在当前进程上下文中替换代码和数据）；

- 将当前进程的用户空间内容替换为新的可执行文件的内容，并在新的进程上下文中启动该可执行文件。

## 组成

- System V exec 函数族由 5 个函数组成，它们分别是 execl，execv，execlp，execvp 和 execle。

```php
int execl(const char *path, const char *arg, ...);
int execv(const char *path, char *const argv[]);
int execlp(const char *file, const char *arg, ...);
int execvp(const char *file, char *const argv[]);
int execle(const char *path, const char *arg, ..., char *const envp[]);
```

1. **l 和 v** 表示参数是以**列表**还是以**数组**的方式提供，都要**以 NULL 结尾**，arg[0] : 需要执行二进制程序的名字；

2. **p** 表示这个函数的第一个参数是 *path，就是以**绝对路径**来提供程序的路径，也可以以**当前目录**作为目标；

3. **e** 表示为程序提供新的**环境变量**，不需要给 NULL；

4. **argv[ ]** : 如执行 ls -al 命令，即 “ls”、“-al”、“NULL”，数组为 **{"ls","-al","NULL"}**；

5. **envp[ ]** : 新环境变量，无为 NULL；
