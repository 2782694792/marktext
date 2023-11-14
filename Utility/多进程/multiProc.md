# 多进程

- 进程是系统进行**资源分配的基本单位**，是程序加载到内存后的执行过程；

- 进程一股由**数据段**，**代码段**和**进程控制块**三部分组成；

- 系统通过进程控制块**感知进程的存在并对进程进行控制**；

- 由于**进程之间虚拟地址空间相互独立**，多进程比多线程更安全，一个进程基本上不会影响另外一个进程；

- 进程三种状态：**就绪、运行、阻塞**。

## fork

**fork** 是一个在 **Linux** 系统环境下专有的函数，现有进程调用 fork 后将会创建一个新的进程。

1. 进程：包括代码、数据、CPU 分配给进程的资源；

2. fork() 函数通过系统调用**创建一个与原来进程几乎完全相同的进程**，也就是两个进程可以做完全相同的事，但如果初始参数或者传入的变量不同，两个进程也可以做不同的事；

3. 调用 fork() 函数后，**系统先给新的进程分配资源**，例如存储数据和代码的空间，然后把原来的进程的所有值都**复制到新的进程**中，只有少数值与原来的进程的值不同。

## 上下文开销

- 上下文保存：进程被暂停时，操作系统需要保存进程的**上下文信息，包括程序计数器、堆栈指针、通用寄存器等**；

- 上下文切换：当一个进程被恢复执行，操作系统需要将该进程的上下文信息恢复，并且**将当前CPU的控制权切换到该进程的代码流**；

- 外部调用：当进程需要调用外部资源时，例如IO操作或者网络通信，操作系统需要将当前进程的**控制权转交**给相应的驱动程序；

- 缓存刷新：当 CPU **切换新进程**时，操作系统需要**刷新 CPU 缓存**。这是因为缓存中存储着特定进程的数据和指令，而切换进程后，缓存中的数据和指令已经无效。

## 进程创建

### 单个子进程

```php
#include <unistd.h>

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

1. 父进程先运行，创建子进程，共享资源，同时共同运行（此时进程运行无固定顺序，由系统的进程调用策略决定），fork 仅**仅被调用一次**，从 fork 创建开始之后，能够**返回两次运行结果**；

2. fork 有三个返回值：父进程中 fork 返回创建子进程的 pid，子进程中 fork 返回 0，异常情况时返回负数；

3. 先结束子进程，再结束父进程；

4. fork 异常，可能有两种原因：
   
   1. 当前的进程数已经达到了系统**规定的上限**，这时 errno 的值为 **EAGAIN**；
   
   2. 系统**内存不足**，errno 的值为 **ENOMEM**。

5. ### **进程之间，资源是独立的**，非共用，存在不同地址中，通过 pid 来识别父子进程。

### 多个子进程

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
I'm 1th child, pid = 20986, ppid = 20985
I'm 2th child, pid = 20987, ppid = 20985
I'm 3th child, pid = 20988, ppid = 20985
I'm 4th child, pid = 20989, ppid = 20985
I'm 5th child, pid = 20990, ppid = 20985
I'm parent, pid = 20985, ppid = 1585
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
            /** getppid() 父进程 */
            /** getpid() 当前进程 */ 
            /** fpid 子进程 PID */
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

6. 循环 fork 开始，不执行则仅仅 1 个进程，执行一次产生 1 个子进程（2 个进程），执行两次产生 3 个子进程（4 个进程），即会有 **2 的 N 次幂**个进程数量。

### 示例

```php
#include <stdio.h> 
#include <unistd.h> 
int main(int argc, char* argv[]) 
{ 
  fork(); ///< 1 2
  fork() && fork() || fork(); ///< 5 10
  fork(); ///< 2 20
  return 0; 
}
```

1. && 与运算：对于左算数，为 0 不进行，不为 0 进行运算；

2. || 或运算：对于左算数，为 0 进行，不为 0 则不进行运算；

3. fork 产生 2 个进程，其中，（1）一个返回大于 0 进行与运算：fork 产生 2 个进程，其中 1 个返回等于 0，进入非运算：产生 2 个进程；总共 3 个进程；（2）一个返回等于 0 进行非运算，产生 2 个进程。总共有 5 个进程。

4. 2 * 5 * 2 为  20 个进程，包括一个主进程 main。

# 分类

[Linux进程4：孤儿进程，僵尸进程(及解决方法)，守护进程讲解_哒宰的自我修养的博客-CSDN博客](https://blog.csdn.net/weixin_40734514/article/details/108990454)

## 孤儿进程

- **父进程先于子进程结束**，则子进程成为孤儿进程；

- 此时子进程的**父进程成为 init 进程**，称为 init 进程领养孤儿进程。

```php
#include <errno.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    pid_t pid;
    pid = fork(); // 创建一个进程
    if (pid < 0) {
        perror("fork error:"); // 创建失败
        exit(1);
    }
    // 子进程
    if (pid == 0) {
        // 输出进程ID和父进程ID
        printf("pid:%d\tppid:%d\n", getpid(), getppid());
        // 睡眠5s，保证父进程先退出
        sleep(5);
        printf("pid:%d\tppid:%d\n", getpid(), getppid());
        printf("child process isexited.\n");
    }
    // 父进程
    else {
        // 父进程睡眠1s，保证子进程输出进程id
        sleep(1);
        printf("father process is exited.\n");
    }
    return 0;
}
```

## 僵尸进程

- 子进程终止，**父进程尚未回收子进程退出状态**，子进程残留资源（PCB）于内核中，变成僵尸进程，以 Z 标识；

- 如果父进程是死循环，那么该僵尸进程就将会**消耗空间**；如果父进程结束，将由 init 进程接手，此时可被清除；

- 僵尸进程是**不能使用 kill 命令清除**，kill 命令只是用来终止进程，而僵尸进程已经终止了；

- 子进程退出，通过**调用 wait 或 waitpid** 获取子进程的状态信息将**避免**僵尸进程；

```cpp
#include <unistd.h>
int main() {
    pid_t pid;
    pid = fork();
    if (pid < 0) {
        perror("fork error:");
        exit(1);
    }
    else if (pid == 0) {
        printf("I am child process.I amexiting.\n");
        exit(0);
    }
    printf("I am father process.I willsleep two seconds\n");
    // 等待子进程先退出
    sleep(2);
    // 输出进程信息
    system("ps -opid,ppid,state,tty,command");
    printf("father process isexiting.\n");
    return 0;
}
```

## 守护进程

- Daemon，是一类**脱离终端在后台执行的程序**，通常以 d 结尾，随系统启动，**父进程通常是 init 进程**。
- 不与任何终端关联的进程，经常以 **root 用户**或其他特殊用户（apache\postfix）运行，**不会被任何终端所产生的的终端信息所打断**；
- 从被执行开始（系统启动时运行），**直到整个系统关闭才结束退出**；
- 父进程 fork 出子进程后先行退出，属于一个**孤儿进程**；
- 非交互程序，没有控制终端。

### 其他概念

- 进程组：一个或多个**进程的集合**，每个进程都有一个进程组 ID；
- 会话期：一个或多个**进程组的集合**，每个会话都有唯一一个会话首进程，会话 ID 为其 ID；
- 控制终端：每个会话可以有一个**单独的控制终端**，**与控制终端连接的会话首进程**就是控制进程。

### 创建步骤

- 调用 **fork()**，创建新进程，它会是将来的**守护进程**；

- 在**父进程中调用 exit**，保证**子进程不是进程组长**；

- 调用 **setsid()** 创建新的会话区，**独立分开**，此后不管控制终端如何，都不会收到信号；

- **更改当前工作目录**成根目录（使得守护进程不与任何文件系统相关联）；

- 将标准输入、标注输出、标准错误重定向到 /dev/null；

```cpp
#include <stdio.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <sys/stat.h> // mode_t umask(mode_t cmask);

int main(void) {
    pid_t pid;
    int   i;
    pid = fork(); // 创建一个新进程,将来会是守护进程
    if (pid == -1) {
        return -1;
    }
    else if (pid != 0) { // 父进程调用exit,保证子进程不是进程组长
        exit(EXIT_SUCCESS);
    }
    
    /** 当进程是会话的进程组长进程时，失败并返回 -1；
      * 调用成功返回新的会话 ID ，使调用进程成为新的会话的领头进程，并脱离父进程会话组和进程组
      */
    if (setsid() == -1)
    {
        fprintf(stderr, "setsid error:%s\n", strerror(errno));
        return -1;
    }
    
    if (chdir("/") == -1) // 使得进程不予任何文件系统联系
    {
        return -1;
    }
    
    for (i = 0; i < NOFILE; i++) {
        close(i); // 关闭所有从父进程继承的不再需要的文件描述符
    }
    
    int oldfd = open("old.txt",O_RDWR | O_CREAT,664);
    if(oldfd < 0)
    {
        perror("open");
    }
    printf("oldfd is : %d\n",oldfd);
    int newfd = dup(oldfd); ///< 标准输入重定向，不继承原文件描述符的属性
    if(newfd < 0)
    {
        perror("dup");
    }
    printf("newfd is : %d\n",newfd);
    char *data = "this is new data";
    write(newfd,data,strlen(data));
    
    // 文件模式创建屏蔽字设置为0
    umask(0); ///< 设置用户、组、其他的读写执行权限
    // 使父进程忽略子进程的信号，不处理子进程的退出
    signal(SIGCHLD,SIG_IGN); // sighandler_t signal ( int signum,  sighandler_t handler );
    
    return 0;
}
```

# exec 函数族

- 在 **Linux** 中，有两组不同的 exec 函数族：System V（以下为例）和 **POSIX**。它们具有相似的函数名，但语法略有不同。

- [Linux进程控制（exec函数族）的理解和使用_呋喃吖的博客-CSDN博客](https://blog.csdn.net/m0_46606290/article/details/123618822)

- [exec函数详解_amoscykl的博客-CSDN博客](https://blog.csdn.net/amoscykl/article/details/80354052)

## 说明

- fork 创建的子进程几乎是父进程的副本，当需要子进程去**执行另外的程序**时，通过 exec 函数族在同一个进程中或不同的程序中执行其他可执行文件；

- exec 函数族可以**根据指定文件名或目录名找到可执行文件**，并用它来取代原调用进程的数据段、代码段和堆栈段（在当前进程上下文中替换代码和数据）；

- 将当前进程的用户空间内容替换为新的可执行文件的内容，并在新的进程上下文中启动该可执行文件。

## 系列函数

- System V exec 函数族由 5 个函数组成，它们分别是 **execl**，**execv**，**execlp**，**execvp** 和 **execle**。

```php
#include <unistd.h>
int execl(const char *path, const char *arg, ...);
int execv(const char *path, char *const argv[]);
int execlp(const char *file, const char *arg, ...);
int execvp(const char *file, char *const argv[]);
int execle(const char *path, const char *arg, ..., char *const envp[]);
```

1. **l (list)和 v (vector)** 表示参数是以**列表**还是以**数组**的方式提供，都要**以 NULL 结尾**，arg[0] : 需要执行二进制程序的名字；

2. **p (path)** 表示这个函数的第一个参数是 *path，就是以**绝对路径**来提供程序的路径，也可以以**当前目录**作为目标；

3. **e (env)** 表示为程序提供新的**环境变量**，不需要给 NULL；

4. **argv[ ]** : 如执行 ls -al 命令，即 “ls”、“-al”、“NULL”，数组为 **{"ls","-al","NULL"}**；

5. **envp[ ]** : 新环境变量，无为 NULL；

6. 函数调用成功则加载新的程序并开始执行，不返回；函数调用**出错，返回 -1**；

## 保留特征

exec 后新进程保持原进程以下特征：

1. 环境变量（使用了execle、execve函数则不继承环境变量）；

2. 进程 ID 和父进程 ID ；

3. 实际用户 ID 和实际组 ID；

4. 附加组 ID；进程组 ID；会话 ID；

5. 控制终端；当前工作目录；根目录；

6. 文件权限屏蔽字；文件锁；

7. 进程信号屏蔽；未决信号；

8. 资源限制；

9. tms_utime、tms_stime、tms_cutime 以及 tms_ustime 值。

## 示例

1. execl、execv、execle 通过 path （可执行文件路径）确定可执行文件执行；

2. PATH 环境变量包含目录表，系统通过该变量定义的路径搜索执行码，目录之间以冒号“:”分隔，以点号“.”结束；

3. Linux 中 Shell 进程是所有执行码的父进程；

4. 使用 execl、execv、execlp、execvp 函数使执行码重生时，Shell 进程会将所有环境变量复制给生成的新进程；

5. 使用 execle、execve 时新进程不继承任何 Shell 进程的环境变量，而由 envp[ ] 数组自行设置环境变量。

<img title="" src="file:///E:/MarkText/image cache/2023-04-28-19-36-13-image.png" alt="" data-align="center">

```cpp
execl("/bin/ls", "ls", "-l", (char *)0);

char *args[] = { "ls", "-l", NULL };
execv("/bin/ls", args);

execlp("ls", "ls", "-l", (char *)0);

char *args[] = { "ls", "-l", NULL };
execvp("ls", args);

char *env[] = { "HOME=/usr/home", "LOGNAME=guest", (char *)0 };
execle("/bin/ls", "ls", "-l", (char *)0, env);
```

[BgroundTools/include/multiProc at master · 2782694792/BgroundTools · GitHub](https://github.com/2782694792/BgroundTools/tree/master/include/multiProc)

# 进程回收

## wait 全局阻塞

- #include <**sys/wait.h**>

- 父进程调用 wait 就**立即阻塞**，由 wait 自动分析是否当前进程的某个子进程已经退出；

- 如果找到了一个已经变成僵尸的子进程，wait 就**收集**它的**信息**，并把它**彻底销毁后返回子进程的 PID**；

- 如果没有找到该类子进程，wait 就会一直阻塞，**直到有一个出现为止**；

- 如果**没有子进程**，wait **返回 -1**。

```cpp
static inline pid_t wait(int * wait_stat){
    return waitpid(-1,wait_stat,0);
}
```

### 成员参数

- 参数 **status** 用来保存被收集进程退出时的一些状态；

- `pid = wait(NULL);` **仅仅消灭僵尸进程**，成功返回 PID，失败返回 -1，同时 errno 置为 ECHILD；

- 如果 status 不为 NULL，子进程退出时的状态存入 status，从而可以进一步明确是正常退出或正常结束以及正常结束的返回值，或是否由信号处理结束以及信号处理结束后的信息。

### 状态宏

- 由于子进程的状态信息保存在整数的不同二进制位中，故选择对应状态判断宏；

- **正常结束**：**WIFEXITED(status)** 是正常退出则返回非零值，仅当 WIFEXITED 正常返回非零值时，通过 **WEXITSTATUS(status)** 获取子进程的返回值，如果子进程调用 exit(9) 则返回 9；

- **信号结束**：**WIFSIGNALED(wstatus)** 如果子进程被信号终止，则返回 true，仅当 WIFSIGNALED 返回 true 时，通过 **WTERMSIG(status)** 返回冬至子进程终止的信号编号；

- **进程暂停**：**WIFSTOPPED(status)** 如果子进程处于暂停状态时，返回非 0，通过 **WSTOPSIG(status)** 获取使得进程暂停的信号编号；

- [wait(2) - Linux manual page (man7.org)](https://www.man7.org/linux/man-pages/man2/waitpid.2.html)。

### 示例

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <unistd.h>

void main_wait() {
    pid_t pid, wpid;
    pid = fork();
    if (pid == -1) {
        perror("fork error");
        exit(1);
    }
    else if (pid == 0) { // 子进程
        printf("child, pid = %d, ppid = %d, sleep 3s\n", getpid(), getppid());
        sleep(3);
        printf("******child die!*******\n");
        exit(9);
    }
    else if (pid > 0) { // 父进程
        // wpid = wait(NULL); // 阻塞 等待回收
        int status;
        wpid = wait(&status); // 阻塞，回收状态信息
        if (wpid == -1) {
            perror("wait error");
            exit(1);
        }

        if (WIFEXITED(status)) {
            printf("child exit with  %d\n", WEXITSTATUS(status));
        }
        if (WIFSIGNALED(status)) { // 信号终止
            printf("child killed by %d\n", WTERMSIG(status));
        }
        printf("parent end! pid = %d\n", getpid());
    }
}
```

## waitpid 指定非阻塞

- 同 wait，但可以**指定 pid** 进程进行清理回收、**非阻塞**；

- 调用 waitpid 和 wait 的作用是完全相同的，但 waitpid 多出了两个可由用户控制的参数：pid 和 options；

```cpp
pid_t waitpid(pid_t  pid, int * status, int options)
```

- 成功，返回子进程 PID，失败（无子进程），返回 -1；

- 如果设置 **WNOHANG**，而 waitpid 发现**没有已退出的子进程可收集，则返回 0**；

- 如果调用中出错，则返回 -1，errno 置为相应的值；

### 成员参数

1. `pid` :  进程 ID；
   1. pid > 0 时，指定子进程，一致等待子进程结束；

   2. pid == -1 时，回收任意子进程；

   3. pid == 0 时，回收和当前调用 waitpid 同组的所有子进程；

   4. pid < -1 时，回收指定进程组内的任意子进程；

2. `options` : 常熟选项，在 Linux 中只支持 **WNOHANG** 和 **WUNTRACED**；

   1. WNOHANG ：**即使没有子进程**退出，也会立即返回，非阻塞；

   2. WUNTRACED ：如果一个子进程已经停止（但没有通过 [ptrace(2) - Linux manual page (man7.org)](https://www.man7.org/linux/man-pages/man2/ptrace.2.html) 跟踪），也返回。即使未指定此选项，也**会为已停止的跟踪子项提供状态**。

### 示例

```cpp
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main()
{
    pid_t pc, pr;
    pc = fork();
    if (pc < 0)
    {
        printf("Error occured on forking.\n");
    }
    else if (pc == 0) // 子进程
    {
        sleep(10);
        exit(0);
    }

    // 父进程
    do {
        pr = waitpid(pc, NULL,
                     WNOHANG); /* 使用了WNOHANG参数，waitpid不会在这里等待 */
        if (pr == 0)
        {
            printf("No child exited\n");
            sleep(1);
        }
    } while (pr == 0);

    if (pr == pc) {
        printf("successfully get child %d\n", pr);
    }
    else
        printf("error\n");
}
```
