---
title: Yet another IO多路复用的博客
date: 2023-03-05 20:35:28
tags: networking
---

网上一堆博客，然后又犯了觉得他们写不清楚的老毛病。

开一篇，也当复习了。所有内容依据网上博客整理，没自己读过源码。有错误请随意指出。

# Select
```c++
int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, timeval *timeout);
```
read, write, except对应需要监听相应事件的fds。

把需要监听的fd扔进`fd_set`（可以当作一个1024位的bitset）里面。有`FD_ZERO`，`FD_SET`，`FD_ISSET`几个宏管理`fd_set`。

调用select时，对于fd_set每一个置1的位对应的fd，select先调用`file_operations->poll`判断文件是否满足监听条件，如果有满足的立刻返回。如果都不满足，把调用进程扔进的等待队列里，然后当前进程进入阻塞态。

select返回以后，fds和timeout被改写。需要用一个循环判断fds里面哪几位是1，然后用对应的fd读写。

pselect是select带一个sigmask的版本。大体是可以在进入pselect之后切换signal的屏蔽（e.g.原来屏蔽sigint，进去以后不屏蔽有特殊处理）。见<https://stackoverflow.com/questions/46042794/how-does-pselect-blocks-signal-using-signal-mask-in-network-programming>。

# Poll
```c++
int poll(pollfd *fds, nfds_t nfds, int timeout);
struct pollfd {
    int fd;			/* File descriptor to poll.  */
    short int events;		/* Types of events poller cares about.  */
    short int revents;		/* Types of events that actually occurred.  */
};
```
显而易见。提前准备好fds数组，填好fd和events，poll结束之后revents会被os填好。

比select好的地方在于fds没有1024位的限制，并且支持更精细的event控制（有一堆宏）。缺点是`pollfd *`是数组，添加新的会...有很头疼的内存分配。提前开好比较大的空间的话，又会因为每次在用户态和内核态中间来回赋值导致耗费比较大。而且和select一样需要遍历所有fds（虽然会好一点点，可以跳过中间没有使用的fd）。

ppoll同pselect。

# Epoll
```c++
int epoll_create(int size); // size is a hint for allocation. returns epfd.
int epoll_ctl(int epfd, int op, int fd, epoll_event *event); // op: add / del / modify
int epoll_wait(int epfd, epoll_event *events, int maxevents, int timeout)
struct epoll_event {
    uint32_t events;	/* Epoll events */
    epoll_data_t data;	/* User data variable. union for ptr or fd. */
};

```
epoll维护就绪队列rdlist链表，用于记录哪个fd就绪（不需遍历）。还维护一个红黑树`map<fd, epoll_event>`（实际还有些别的，只看接口的话可以看成这样）。（红黑树node和链表node共用，所以删的时候是一起删掉的）。

epoll_create打开一个需要close掉的fd用来维护epoll。这玩意和文件一样有等待队列，稍后用到。

epoll_ctl增删改epoll监听的fd。具体是把epoll本体添加到对应fd的等待队列里。fd对应的文件就绪时，把自己加进rdlist，然后让epoll就绪。此外，把fd->epoll_event的关系添加进红黑树，方便在fd就绪时查找对应的节点。

调用epoll_wait时如果rdlist非空立刻返回，为空则阻塞，并把当前进程加入epoll的等待队列，epoll就绪时返回。返回时需要分配一段内存放copy过来的epoll_event。

看了一点源码，红黑树里面装的epoll_event是epoll_ctl设置的原始值。epoll_wait返回时，会往传入的event里面copy一份原始值，然后去到fd对应的文件里拿真正发生的events做一个and。

# aio
没看。目前在linux的实现貌似不全面而且有问题？反正是给kernel提供一块user space的空间，kernel把东西全搞定以后发通知回来。
