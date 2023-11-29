## Linux 串口编程



- 一切皆为**文件**；
- 头文件：#include<**termios.h**>、#include<**fcntl.h**>
- 初始化阶段：设置波特率、停止位、奇偶校验位等；



### 步骤



1. **open**() 打开串口；
2. 串口初始化；
3. **write()/read()** 发送和接收数据；
4. **close**() 关闭串口；



### 打开串口



- 头文件：**fcntl.h** 和 **unistd.h；**
- 遵循 [POSIX](https://baike.baidu.com/item/可移植操作系统接口/12718298?fromtitle=POSIX&fromid=3792413&fr=aladdin)（可移植操作系统接口）标准；
- Linux 中的所有 open 类的函数比如 **fopen()/fclose()** 都是在 open() / close() 的基础上封装；



```c
#incude <fcntl.h>
// 成功，返回文件描述符 fd（整数），否则为 -1
int open(const char* file, int oflags);
int open(const char* file, int oflags, mode_t mode);


#incude <unistd.h>
// 成功，返回 0, 否则返回 -1，关闭一个已关闭的描述符会出错
int close(int fd); 

// 示例：
fd = open("/dev/ttyUSB0", O_RDWR | O_NOCTTY | O_EXCL | O_NONBLOCK );
if (0 > fd) {
	perror("open error");
	return -1;
}
```

options:

1. file：串口名称，一般 Linux 系统中，串口（包括 USB 转串）都在 **/dev** 目录下，设备节点名称有 **tty(n)、ttyUSB(n)、ttyS(n) 以及其他名称**；
2. oflags：打开模式，可组合标志位进行串口打开控制；
3. mode：有**新建文件时**才填写，用 **8 进制**的数（与 umask 有关）代表新建文件的权限；（Linux 终端中输入 **umask** 即可查看本地 umask 权限值，通过 umask 可以**获取本地的掩码或者修改本地的掩码**）



| O_RDONLY / O_WRONLY/ O_RDWR | 只读 、只写、可读可写；使用时只能使用一种                    |
| --------------------------- | ------------------------------------------------------------ |
| O_APPEND                    | 追加写                                                       |
| O_CREAT                     | 如果指定文件不存在，则创建这个文件；使用时，需要同时说明第三个参数 mode，说明新文件的存取许可权限 |
| O_EXCL                      | 如果要创建的文件已存在，则返回 -1，并且修改errno的值         |
| O_TRUNC                     | 如果文件存在，若以只写/读写方式打开，则清空文件全部内容      |
| O_NOCTTY                    | 该标志用于告知系统它不会成为进程的控制终端。如果不指定该标志，任何输入都会影响程序（比如键盘中止信号等） |
| O_NONBLOCK                  | 如果路径名指向 FIFO/块文件/字符文件，则把文件的打开和后继 I/O 设置为非阻塞模式（nonblocking mode） |
| O_NDELAY                    | 告诉内核这个程序不关注 DCD 信号线的状态。如果不指定该标志，当 DCD 信号线是空电压值的时候，程序将会进入睡眠 |
| O_SYNC                      | 同步方式写入                                                 |
| O_DSYNC                     | 提供同步的I/O数据完整性                                      |
| O_ASYNC                     | 当I/O操作可行，产生信号通知进程                              |
| O_DIRECT                    | 无缓冲输入输出                                               |

- **DCD**（Data Carrier Detect，**载波检测**），作用是 Received Line Signal Detector，即 **接收线信号检出**。当本地的 **DCE**（Data Communications Equipment，**数据通信设备**）收到由通信链路另一端的 DCE 送来的载波信号时，使 DCD 变为有效(**高电平**)，表示已检测出远端的载波信号，要求数据终端设备**(DTE)准备接收**。
- DCD 信号通常来自串口连结线的另一端。这条信号线上的 **space 电压表示另一端的电脑或者设备现在已经连接**。但是，DCD 信号线却不是总可以得到的，有些设备上有这条信号线，而有的则没有。串口通信中一般有这条线。



### 初始化

#### 设备管理

```c
#include <sys/ioctl.h> 
/**
 * fd: 文件描述符
 * cmd: 交互协议, 设备驱动将根据cmd执行对应操作
 * ...: 可变参数args, 依赖cmd指定长度以及类型
 */
int ioctl(int fd, unsigned long request, ...);

int l_iStatus2;
l_iRet = ioctl(m_iHandle, TIOCMGET, &l_iStatus2); // 获取串口的 modem 控制信号

// 设置，设置或清除 RTS（Ready to Send）、DTR（Data Terminal Ready）等控制信号
l_iRet = ioctl(m_iHandle, TIOCMSET, &l_iStatus2);

// 调用串口设备的TCSBRK命令来发送break信号，当参数为非零值时，TCSBRK 会发送一个 break 信号
l_iRet = ioctl(m_iHandle, TCSBRK, 1);

int l_iBytesNotRead = 0;
ioctl(m_iHandle, FIONREAD, &l_iBytesNotRead); // 获取当前串口输入缓冲区中可读取的字节数
```

#### termios 结构体

- 头文件 **termios.h**；
- 结构体中定义了设置串口有关的参数，**tcsetattr()** 函数用来根据 termios 结构体的设置来配置；

```c
// 配置为原始模式
struct termios new_cfg;
cfmakeraw(&new_cfg);

typedef unsigned char	cc_t;
typedef unsigned int	speed_t;
typedef unsigned int	tcflag_t;
#define NCCS 32

struct termios{
    tcflag_t c_iflag;    // 输入模式标志。控制终端驱动程序从串口或键盘接收到的字符数据在被传递给应用程序之前的处理方式。
    tcflag_t c_oflag;    // 输出模式标志。控制由应用程序发送出去的字符数据在传递到串口或屏幕之前是如何处理的。
    tcflag_t c_cflag;    // 控制模式标志。对串口来说可以设置波特率，数据位、校验位、停止位的宽度、校验位等
    tcflag_t c_lflag;    // 本地模式用于控制终端的本地数据处理和工作模式。
    cc_t     c_line;     // 线路规程
    cc_t     c_cc[NCCS];  // 特殊控制字符。特殊控制字符是一些字符组合，如 Ctrl+C、 Ctrl+Z 等， 当用户键入这样的组合键，终端会采取特殊处理方式。
    speed_t  c_ispeed;   // 终端的输入速度
    speed_t  c_ospreed;  // 终端的输出速度
#define _HAVE_STRUCT_TERMIOS_C_ISPEED 1
#define _HAVE_STRUCT_TERMIOS_C_OSPEED 1
};
```

struct termios l_tTermios;

##### c_iflag

- **输入模式**标志位；
- 设备接收数据的方式：决定了串口对接收到数据的处理以及传递；

```cpp
#define IGNBRK 0000001 // 忽略输入终止条件
#define BRKINT 0000002 // 当检测到输入终止条件时发送SIGINT信号
#define IGNPAR 0000004 // 忽略奇偶校验错误

#define PARMRK 0000010 // 奇偶校验错误掩码
#define INPCK  0000020 // 奇偶校验使能
#define ISTRIP 0000040 // 裁剪掉第8比特，将所有接收到的数据裁剪为 7 比特位

#define INLCR  0000100 // 将接收到的 NL（换行）映射到 CR（回车）
#define IGNCR  0000200 // 忽略接收到的 CR
#define ICRNL  0000400 // 将接收到的 CR 映射到 NL

#define IUCLC  0001000 // 将接收到的大写字符映射到小写字符 (not in POSIX)
#define IXON   0002000 // 使能输出软件控制流
#define IXANY  0004000 // 输入任意字符可以重新启动输出

#define IXOFF  0010000 // 使能输入软件控制流
#define IMAXBEL 0020000 // 当输入队列满时响铃（ring bell）(not in POSIX)
#define IUTF8  0040000 // 输入是 UTF8 (not in POSIX)
```



##### c_oflag

- **输出模式**标志位；
- 控制输出字符的处理方式，即由程序发出的字符在传递到串行口或屏幕之前如何处理；

```c
#define OPOST  0000001 // 启动输出处理功能，如果不设置该位，将忽略下面介绍的所有设置
#define OLCUC  0000002 // 将输出中的大写字符转换成小写字符(not in POSIX)
#define ONLCR  0000004 // 将输出中的换行符(NL '\n')转换成回车符(CR '\r')

#define OCRNL  0000010 // 将输出中的回车符转换成换行符
#define ONOCR  0000020 // 如果当前列号为0，则不输出回车字符
#define ONLRET 0000040 // 不输出回车符

#define OFILL  0000100 // 发送填充字符以提供延时
#define OFDEL  0000200 // 如果设置该标志，则表示填充字符为 DEL 字符，否则为 NULL 

#define NLDLY  0000400 // 换行符(newline)延时掩码
#define CRDLY  0003000 // 回车符(carriage-return)延时掩码
#define TABDLY 0014000 // 制表符(horizontal-tab)延时掩码
#define BSDLY  0020000 // 退格符(backspace)延时掩码
#define FFLDY  0100000 // 换页符(form-feed)延时掩码
#define VTDLY  0040000 // 垂直制表符(vertical-tab)延时掩码
```



##### c_cflag

- **控制模式**标志位可选参数，16 位；
- 可以用来设置 **波特率**，**数据位**、**校验位**、**停止位的宽度、校验位** 等；
- 需要用**位操作**来设置或清除相应的位；
- 控制终端的硬件特性；

###### 流控制

```cpp
l_tTermios.c_cflag &= ~CRTSCTS; // 不使用流控制
l_tTermios.c_cflag |= CRTSCTS; // 使用硬件流控制
l_tTermios.c_iflag |= IXON | IXOFF | IXANY; // 使用软件流控制
```

###### 字符大小位

```cpp
l_tTermios.c_cflag &= ~CSIZE; // 屏蔽字符大小位
```

###### 波特率

- 设置 / 返回输入波特率：**cfsetispeed() / cfgetispeed()**；
- 设置 / 返回输出波特率：**cfsetospeed() / cfgetospeed()；**
- 设置波特率：成功返回 0，否则返回 -1；

```c
int cfsetispeed(struct termios *termptr, speed_t speed);
speed_t cfgetispeed(const struct termios *termptr);
int cfsetospeed(struct termios *termptr, speed_t speed);
speed_t cfgetospeed(const struct termios *termptr);

// 使用 CBAUD 位掩码所选择的位来指定串口波特率
#define  B0	      0000000 // 挂起
#define  B50	  0000001 // 50 波特率
#define  B75	  0000002
#define  B110	  0000003
#define  B134	  0000004
#define  B150	  0000005
#define  B200	  0000006
#define  B300	  0000007
#define  B600	  0000010
#define  B1200	  0000011
#define  B1800	  0000012
#define  B2400	  0000013
#define  B4800	  0000014
#define  B9600	  0000015
#define  B19200	  0000016
#define  B38400	  0000017
#define  CBAUD    0001017  // 波特率的位掩码 (not in POSIX)
#define  B57600   0010001
#define  B115200  0010002
#define  B230400  0010003
#define  B460800  0010004
#define  B500000  0010005
#define  B576000  0010006
#define  B921600  0010007
#define  B1000000 0010010
#define  B1152000 0010011
#define  B1500000 0010012
#define  B2000000 0010013
#define  B2500000 0010014
#define  B3000000 0010015
#define  B3500000 0010016
#define  B4000000 0010017
#define __MAX_BAUD B4000000
```



###### 停止位

```c
switch (sComInfo.iStopbits)
{
case ONESTOPBIT:
	l_tTermios.c_cflag &= ~CSTOPB; ///< 一个停止位
	break;
case TWOSTOPBITS:
	l_tTermios.c_cflag |= CSTOPB; ///< 两个停止位
	break;
default:
	close(m_iHandle);
	m_iHandle = -1;
}

// 停止位，设置该值则为2个停止位，不设置则为1个停止位
#define CSTOPB	0000100
```





###### 数据位

```c
l_tTermios.c_cflag &= ~CSIZE; // 字符长度的位掩码。置为 0，使用默认的 8 位数据位
switch (sComInfo.iDatabits)
{
case 7:
	l_tTermios.c_cflag |= CS7;
	break;
case 8:
	l_tTermios.c_cflag |= CS8;
	break;
default:
	close(m_iHandle);
	m_iHandle = -1;
}

// 数据位
#define   CS5	0000000  // 5 位数据位
#define   CS6	0000020  // 6 位数据位
#define   CS7	0000040  // 7 位数据位
#define   CS8	0000060  // 8 位数据位
```



###### 校验位

```cpp
switch (sComInfo.iParity)
{
case NOPARITY:  //none
case '0':  //none
case 'n':  //none
case 'N':  //none
	l_tTermios.c_cflag &= ~PARENB; // 无奇偶校验
	l_tTermios.c_iflag &= ~INPCK;
	break;
case ODDPARITY:  //odd
case '1':  //none
case 'o':  //odd
case 'O':  //odd
	l_tTermios.c_cflag |= (PARODD | PARENB); // 奇校验
	l_tTermios.c_iflag |= INPCK;
	break;
case EVENPARITY:  //even
case '2':  //none
case 'e':  //even
case 'E':  //even
	l_tTermios.c_cflag |= PARENB;
	l_tTermios.c_cflag &= ~PARODD; // 偶校验
	l_tTermios.c_iflag |= INPCK;
	break;
default:
	close(m_iHandle);
	m_iHandle = -1;
}

// 使能奇偶校验位
#define PARENB	0000400

// 设置则使用奇校验，否则使用偶校验
#define PARODD	0001000
```

###### 输出模式

```cpp
l_tTermios.c_cflag &amp;= ~OPOST; // 原始数据（RAW）输出
```

###### 调制解调器

```c
l_sOptionCurrent.c_cflag |= (CLOCAL | CREAD );

// 接收使能，这个位和 CLOCAL 都要在开始时使能的
#define CREAD	0000200

// 本地连接(不改变端口所有者)
#define CLOCAL	0004000
```



##### c_lflag

- **本地模式**标志位；
- 控制终端的某些特性控制终端的某些特性；
- **规范模式**下，输入终端读到了行结束标志时，输入缓冲区的数据才被送到 read() 函数完成读取；
- **非规范模式**下，所有的输入都是即时有效的，不需要用户另外输入行结束符，且不可进行行编辑；

```c
l_tTermios.c_lflag &= ~ECHO; // 关闭回终端回显功能
l_tTermios.c_lflag |= ECHONL;

#define ISIG   0000001  // 若收到信号字符(INTR,QUIT等)则会产生相应的信号
#define ICANON 0000002  // 启动规范(canonical)模式

#define ECHO   0000010  // 启动输入字符的本地回显功能
#define ECHOE  0000020  // 若设置ICANON，则允许退格操作
#define ECHOK   0000040 // 若设置ICANON，则KILL字符会删除当前行

#define ECHONL  0000100 // 若设置ICANON，则允许回显换行符
#define NOFLASH 0000200 // 在通常情况下，当接收到INTR，QUIT，SUSP控制字符时，会清空输入和输出队列。如果设置该标志，则所有的队列不会被清空
#define TOSTOP  0000400 // 若一个后台进程试图向它的控制终端进行写操作，则系统向该后台进程的进程组发送SIGTTOU信号。该信号通常终止进程的执行

#define ECHOCTL 0001000 // 若设置ECHO，则控制字符 Y(Y可以是回车符、制表符等)会显示成 "^X" 的样子,其中 "X" 的ASCII码等于给相应的控制字符的 ASCII 码加上 0x40。
                        // 例如，退格字符（0x08）会显示为“^H”('H'的 ASCII 码为 0x48)。这与我们在终端中按下 Ctrl+C 后会显示一个 "^C" 类似。
#define ECHOPRT 0002000 // 若设置ICANON和IECHO, 则删除字符和被删除的字符都会被显示
#define ECHOKE  0004000 // 若设置ICANON, 则允许回显在ECHOE和ECHOPRT中设定的KILL字符

#define IEXTEN  0100000 // 启动输入处理功能
```

##### c_cc[NCC]

- 特殊控制字符；
- 用于保存终端驱动程序中的特殊字符，如输入结束符（ctrl+c）等；

```c
new_cfg.c_cc[VTIME] = 0; ///< 非阻塞
new_cfg.c_cc[VMIN] = 0; ///< read()调用都会立即返回


#define VINTR  0 // (Ctrl-C) 中断控制字符。
                 // 该字符使终端驱动程序向与终端相连的进程以送SIGINT信号  
#define VQUIT  1 // (Ctrl-Z) 退出操作符。
                 // 该字符使终端驱动程序向与终端相连的进程发送SIGQUIT信号
#define VERASE 2 // (Backspace) 删除操作符。
                 // 该字符使终端驱动程序删除输入行中的最后一个字符，但不删除上一个EOF或行首。
                 // 当设置 ICANON 时可被识别，不再作为输入传递。  
#define VKILL  3 // (Ctrl-U) 删除行符。
                 // 删除输入行，即删除自上一个 EOF(文件结束符) 或行首以来的输入。
                 // 当设置 ICANON 时可被识别，不再作为输入传递。
#define VEOF   4 // (Ctrl-D) 文件结束符。
                 // 这个字符使得缓冲中的内容被送到等待输入的用户程序中，而不必等到 EOL。如果它
                 // 是一行的第一个字符，那么用户程序的 read() 将返回 0, 表示文件结束，即读到了EOF。 
                 // 当设置 ICANON 时可被识别，不再作为输入传递。

#define VTIME  5 // 在非规范模式下，指定读取的每个字符之间的超时时间，以十分之一秒为计时单位。
#define VMIN   6 // 在非规范模式下，指定最少读取的字符数。  

#define VSWTC  7 // 开关字符。(not in POSIX) 
#define VSTART 8 // (Ctrl-Q) 开启字符。重新开始被 STOP 字符中止的输出。
#define VSTOP  9 // (Ctrl-S) 停止字符。停止输出，直到键入 START 字符。 
#define VSUSP 10 // (Ctrl-Z) 挂起字符。向与终端相连的进程发送 SIGTSTP 信号，用于挂起当前的应用程序
#define VEOL  11 // 附加的行尾字符 EOL。对应键为 Carriage return(CR), 作用类似于行结束符。
                 // 当设置 ICANON 时可被识别。
#define VREPRINT 12 // (Ctrl-R) 重新输出未读的字符。
                    // 当设置 ICANON 和 IEXTEN 时可被识别，不再作为输入传递。  
#define VDISCARD 13 // (Ctrl-O) 开关：开始/结束丢弃未完成的输出。
                    // 当设置IEXTEN 时可被识别，不再作为输入传递。 
#define VWERASE  14 // (Ctrl-W) 删除词。
                    // 当设置 ICANON 和 IEXTEN 时可被识别，不再作为输入传递。  
#define VLNEXT   15 // (Ctrl-V) 字面上的下一个。引用下一个输入字符，取消它的任何特殊含义。
                    // 当设置IEXTEN 时可被识别，不再作为输入传递。  
#define VEOL2    16 // 第二行结束字符。，对应键为 Line feed(LF)。
                    // 当设置ICANON 时可被识别。  
```

- 对于 **VTIME** 和 **VMIN** 的处理：

- - **均为 0** 时，read 调用则**立刻返回**。如果没有字符等待处理则返回 0，并不读取任何字符；
  - **VMIN = 0** 和 **VTIME > 0** 时，只要有字符可以处理或者经过 VTIME 个十分之一秒的时间间隔时，read 调用即返回。如果因为超时而未读到任何字符，read 返回 0，否则 read **返回读取的字符数目**；
  - **VMIN > 0** 和 **VTIME = 0** 时，read 调用将一直等待，直到有 MIN 个字符可读取时才返回，返回读取的字符数量。到达文件尾时返回 0；
  - **VMIN > 0** 和 **VTIME > 0** 时，当有 MIN 个字符可读或两个输入字符之间的时间间隔超过 VTIME 个十分之一秒时，read 才返回。因为**在输入第一个字符后系统才会启动定时器**，所以，在这种情况下， read() 函数**至少读取一个字节**后才返回。

#### 串口属性配置

- **tcsetattr()**：设置串口的结构属性；
- **tcgetatt()**：获取串口的结构属性。

```c
/** 成功返回 0，否则返回 -1 */
int tcgetattr(int fd, struct termios *termios_p);

int tcsetattr(int fd, int optional_actions, const struct termios *termios_p);

/// options: optional_actions
#define	TCSANOW		0  
// 所有写入 fd 的输出都被传输后生效。用于修改影响输出的参数时使用(当前输出完成时将值改变) 
#define	TCSADRAIN	1 
// 所有写入 fd 引用的对象的输出都被传输后生效，所有已接受但未读入的输入都在改变发生前丢弃(同TCSADRAIN，但会舍弃当前所有值)
#define	TCSAFLUSH	2  
```

#### I/O 缓冲区

- 调用 tcsetattr() 之前进行选择性地调用 **tcflush()** 清空 I/O 缓冲区；

```c
int tcdrain(int fd); ///< 阻塞程序，直到 输出缓冲区 数据全部发送完毕为止
int tcflush(int fd, int queue_selector); ///< 清空输入 / 输出缓冲区的数据
int tcflow(int fd, int action); ///< 暂停数据传输或接收工作

#define TCIFLUSH 0   // 清空输入缓冲区
#define TCOFLUSH 1   // 清空输出缓冲区
#define TCIOFLUSH 2  // 清空输入和输出缓冲区

/**
 * TCOOFF	暂停数据输出（输出传输）；
 * TCOON	重新启动暂停的输出；
 * TCIOFF	发送STOP 字符，停止终端设备向系统发送数据；
 * TCION	发送一个START 字符，启动终端设备向系统发送数据；
 */
```

### 发送 / 接收

- 头文件：**unistd.h**；
- **write()/read()**：发送 / 接收；

```c
// 返回值是读/写的数据的数量，如果发生错误则返回 -1，如果读/写到 EOF 则返回 0。
ssize_t read(int fd, void* buf, size_t nbytes);
ssize_t write(int fd, const void* buf, size_t nbytes);
```

#### 轮询

```c
int n = 0;
while (1)
{
    n = read(fd, buf, sizeof(buf));
    if (n > 0) {
        printf("Received: %.*s\n", n, buf);
    }

    strcpy(buf, "Hello, world!\n");
    n = write(fd, buf, strlen(buf));
    if (n < 0) {
        perror("write failed\n");
    }
    usleep(10 * 1000);
}
```

#### 中断读取

- 避免轮询；
- 避免数据包粘包；

```c
DWORD l_dwTime = dwGetTickCount();
while (dwGetTickCount() - l_dwTime < p_uiTimeout) // 超时
{
    int rc = -1;
    fd_set fds;
    struct timeval tv;
    FD_ZERO(&fds);
    FD_SET(m_iHandle,&fds);
    tv.tv_sec = 0;
    tv.tv_usec = 500;
    
    // 需要监视的文件描述符集合中最大的文件描述符值加 1 : select() 在监视文件描述符时需要遍历的范围
    rc = select(m_iHandle + 1, &fds, NULL, NULL, &tv); 
	if(rc >= 0)
	{
		int l_iBytesNotRead = 0;
		ioctl(m_iHandle, FIONREAD, &l_iBytesNotRead); // 获取设备输入缓冲区中可读取的字节数
		if (l_iBytesNotRead > 0)
		{
       		int l_iResult = read(m_iHandle, &l_acRevData[l_uiRecvLength], p_uiRecvLength);
			if (l_iResult < 0) // 读取指定长度的字节
			{
				continue;
			}
			else
			{
				l_uiRecvLength += l_iResult;
			}
		}

		if (l_uiRecvLength>=p_uiRecvLength)
		{
			break;
		}
	}
	//vDelay(10);
}
```

#### 信号收发

```c
#include <iostream>
#include <csignal>
#include <cstring>
#include <unistd.h>
#include <fcntl.h>
#include <termios.h>
#include <cstdlib>

// 串口文件描述符
int serial_fd;

// 信号处理函数
void sigio_handler(int /*sig*/) {
    char buffer[255];
    int bytes_read = read(serial_fd, buffer, sizeof(buffer));
    if (bytes_read > 0) {
        std::cout << "Received data: " << std::string(buffer, bytes_read) << std::endl;
    }
}

int main() {
    // 打开串口
    serial_fd = open("/dev/ttyS0", O_RDWR | O_NOCTTY | O_NONBLOCK);
    if (serial_fd == -1) {
        std::cerr << "Failed to open serial port" << std::endl;
        return 1;
    }

    // 配置串口
    struct termios tio;
    memset(&tio, 0, sizeof(tio));
    tcgetattr(serial_fd, &tio);
    cfsetospeed(&tio, B9600);  // 设置波特率为 9600
    cfsetispeed(&tio, B9600);
    tio.c_cflag = (tio.c_cflag & ~CSIZE) | CS8;  // 8 位数据位
    tio.c_cflag &= ~(PARENB | PARODD);  // 无奇偶校验
    tio.c_cflag &= ~CSTOPB;  // 1 位停止位
    tcsetattr(serial_fd, TCSANOW, &tio);

	// 设置为当前进程的拥有者，从而接收该文件描述符相关的信号
    fcntl(serial_fd, F_SETOWN, getpid());
    // 设置串口文件描述符为异步通知模式
    fcntl(serial_fd, F_SETFL, O_ASYNC);

    // 注册 SIGIO 信号的处理函数
    struct sigaction sa;
	// 设置了 SIGIO 信号的处理函数 sigio_handler，在该信号被触发时读取串口数据并进行处理
    sa.sa_handler = sigio_handler;
    // 将信号处理函数在执行时要屏蔽的信号集合清空
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    sigaction(SIGIO, &sa, NULL);

    // 模拟发送数据
    const char *data = "Hello, this is a test message.";
    write(serial_fd, data, strlen(data));

	while (1) {
	    // 等待 SIGIO 信号
	    sleep(1);
	} // 等待一段时间模拟串口数据的接收和处理

    // 关闭串口
    close(serial_fd);

    return 0;
}
```

#### 线程接收

```c++
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <termios.h>
#include <pthread.h>
 
void *read_thread(void *arg) {
    int fd = *(int *)arg;
    char buf[256];
    int n;
 
    while (1) {
        // 读取串口数据
        n = read(fd, buf, sizeof(buf));
        if (n > 0) {
            printf("Received data: %.*s\n", n, buf);
        }
    }
 
    return NULL;
}
 
int main() {
    int fd;
    struct termios options;
    pthread_t tid;
 
    // 打开串口设备
    fd = open("/dev/ttyS0", O_RDWR | O_NOCTTY);
    if (fd < 0) {
        perror("open");
        return -1;
    }
 
    // 配置串口参数
    tcgetattr(fd, &options);
    options.c_cflag = B9600 | CS8 | CLOCAL | CREAD;
    options.c_iflag = IGNPAR;
    options.c_oflag = 0;
    options.c_lflag = 0;
    options.c_cc[VTIME] = 0;
    options.c_cc[VMIN] = 1;
    tcsetattr(fd, TCSANOW, &options);
 
    // 创建读取线程
    if (pthread_create(&tid, NULL, read_thread, &fd) != 0) {
        perror("pthread_create");
        return -1;
    }
 
    while (1) {
        // 主线程的其他处理逻辑
        sleep(1);
    }
 
    // 关闭串口设备
    close(fd);
 
    return 0;
}
```