## 1、线程通信

`ManualResetEvent` 是 C# 中一个比较常用的工具，可用于线程间通信，实现一种类似信号量的功能.

### 1.1 初始化

```cpp
public ManualResetEvent(bool initialState);
```

ManualResetEvent的构造方法有个bool型参数，当为true时，则表示有信号，为false时，则表示无信号。

### 1.2 线程阻塞

```cpp
public virtual bool WaitOne ();
```

阻塞当前线程，直到当前 WaitHandle 收到信号。如果当前实例接收到信号，则为 true。如果当前实例从未发出信号，则 WaitOne() 永远不会返回。

> 当初始化ManualResetEvent时，initialState为false，WaitOne将会有阻塞效果，否则，没有阻塞效果。

### 1.3 信号状态设置

```cpp
ManualResetEvent::set();
```

Set方法：将ManualResetEvent对象的信号状态设为有信号状态，这个时候WaitOne如果正在阻塞中的话，将会立即终止阻塞，向下继续执行。而且这个状态一直不变的话，每次执行到WaitOne都将无任何阻塞。

### 1.4 信号重置

```cpp
ManualResetEvent::Reset();
```

将ManualResetEvent对象的信号状态设为无信号状态，当下次执行到WaitOne时，又将重新开始阻塞。
