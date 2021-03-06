## linux指令

### select与epoll区别

#### select

事件通知，无差别轮询。同时观察许多流的I/O事件，当空闲时就会阻塞线程，每当有一个流有I/O事件，就从阻塞态中醒来并轮询一遍所有的流。事件复杂度是$O(n)$

#### epoll

epoll代理：事件通知，再有针对性的处理事件

epoll之会把哪个流发生了怎样的I/O事件通知我们。此时我们对这些流的操作都是有意义的。（复杂度降低到了O(k)，k为产生I/O事件的流的个数，也有认为O(1)的[原文为O(1)，但实际上O(k)更为准确]）

1）调用epoll_create建立一个epoll对象，这个对象包含了一个红黑树和一个双向链表。并与底层建立回调机制。
2）调用epoll_ctl向epoll对象中添加这100万个连接的套接字
3）调用epoll_wait收集发生事件的连接。

epoll的高效就在于，当我们调用epoll_ctl往里塞入百万个句柄时，epoll_wait仍然可以飞快的返回，并有效的将发生事件的句柄给我们用户。这是由于我们在调用epoll_create时，内核除了帮我们在epoll文件系统里建了个file结点，在内核cache里建了个红黑树用于存储以后epoll_ctl传来的socket外，还会再建立一个list链表，用于存储准备就绪的事件，当epoll_wait调用时，仅仅观察这个list链表里有没有数据即可。有数据就返回，没有数据就sleep，等到timeout时间到后即使链表没数据也返回。所以，epoll_wait非常高效。水平触发(level-trggered)

只要文件描述符关联的读内核缓冲区非空，有数据可以读取，就一直发出可读信号进行通知，
 当文件描述符关联的内核写缓冲区不满，有空间可以写入，就一直发出可写信号进行通知
 LT模式支持阻塞和非阻塞两种方式。epoll默认的模式是LT。

#### 边缘触发(edge-triggered)

当文件描述符关联的读内核缓冲区由空转化为非空的时候，则发出可读信号进行通知，
 当文件描述符关联的内核写缓冲区由满转化为不满的时候，则发出可写信号进行通知
 两者的区别在哪里呢？水平触发是只要读缓冲区有数据，就会一直触发可读信号，而边缘触发仅仅在空变为非空的时候通知一次，

### 僵尸进程

在 Linux 系统中,一个进程结束了,但是他的父进程没有等待(调用wait / waitpid)他,那么他将变成一个僵尸进程。当用ps命令观察进程的执行状态时,看到这些进程的状态栏为defunct。僵尸进程是一个早已死亡的进程,但在进程表(processs table)中仍占了一个位置(slot)。
但是如果该进程的父进程已经先结束了,那么该进程就不会变成僵尸进程。因为每个进程结束的时候,系统都会扫描当前系统中所运行的所有进程,看看有没有哪个进程是刚刚结束的这个进程的子进程,如果是的话,就由Init进程来接管他,成为他的父进程,从而保证每个进程都会有一个父进程。而Init进程会自动wait其子进程,因此被Init接管的所有进程都不会变成僵尸进程。

### TOP

top命令能够清晰的展现出系统的状态，而且它是实时的监控，按q退出。

Cpu(s)行展示了当前CPU的状态，us表示用户进程占用CPU比例，sy表示内核进程占用CPU比例，id表示空闲CPU百分比，wa表示IO等待所占用的CPU时间的百分比。**wa占用超过30%则表示IO压力很大**。

### lsof

lsof(list open files)是一个列出当前系统打开文件的工具。

lsof 查看端口占用语法格式：

```
lsof -i:端口号
```