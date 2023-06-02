# 细谈事件驱动--epoll

epoll 提供了三个函数：

- int epoll_create(int size);
   建立一個 epoll 对象，并传回它的id
- int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
   事件注册函数，将需要监听的事件和需要监听的fd交给epoll对象
- int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
   等待注册的事件被触发或者timeout发生

epoll解决的问题：

- epoll没有fd数量限制
   epoll没有这个限制，我们知道每个epoll监听一个fd，所以最大数量与能打开的fd数量有关，一个g的内存的机器上，能打开10万个左右
- epoll不需要每次都从user space 将fd set复制到内核kernel
   epoll在用epoll_ctl函数进行事件注册的时候，已经将fd复制到内核中，所以不需要每次都重新复制一次
- select 和 poll 都是主動輪詢機制，需要拜訪每一個 FD；
   epoll是被动触发方式，给fd注册了相应事件的时候，我们为每一个fd指定了一个回调函数，当数据准备好之后，就会把就绪的fd加入一个就绪的队列中，epoll_wait的工作方式实际上就是在这个就绪队列中查看有没有就绪的fd，如果有，就唤醒就绪队列上的等待者，然后调用回调函数。
- 虽然epoll。poll。epoll都需要查看是否有fd就绪，但是epoll之所以是被动触发，就在于它只要去查找就绪队列中有没有fd，就绪的fd是主动加到队列中，epoll不需要一个个轮询确认。
   换一句话讲，就是select和poll只能通知有fd已经就绪了，但不能知道究竟是哪个fd就绪，所以select和poll就要去主动轮询一遍找到就绪的fd。而epoll则是不但可以知道有fd可以就绪，而且还具体可以知道就绪fd的编号，所以直接找到就可以，不用轮询。

# 总结

- select, poll是为了解決同时大量IO的情況（尤其网络服务器），但是随着连接数越多，性能越差
- epoll是select和poll的改进方案，在 linux 上可以取代 select 和 poll，可以处理大量连接的性能问题
