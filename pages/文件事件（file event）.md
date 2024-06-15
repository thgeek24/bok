- What
	- Redis 基于[[Reactor模式]]开发了自己的网络事件处理器，也就是文件事件处理器。文件时间处理器采用[[IO 多路复用技术]]，同时监听多个套接字（socket），并为套接字关联不同的事件处理函数，当套接字的可读或可写事件触发时，就会调用相应的事件处理函数
- Why
- How
	- 为什么单线程的 Redis 能那么快
		- Redis的瓶颈主要在IO而不是CPU，所以为了省开发量，在6.0版本前是单线程模型；其次，Redis 是单线程主要是指 **Redis 的网络 IO 和键值对读写是由一个线程来完成的**，这也是 Redis 对外提供键值存储服务的主要流程。（但 Redis 的其他功能，比如持久化、异步删除、集群数据同步等，其实是由额外的线程执行的）
		- 采用了[[IO 多路复用技术]]使其在网络 IO 操作中能并发处理大量的客户端请求，实现高吞吐率
	- Redis 事件响应框架 ae_event 和文件事件处理器
		- ae_event
			- Redis 并没有使用 libevent 或 libev 这样的成熟开源方案，而是自己实现一个非常简洁的事件驱动库 ae_event
			- Redis 使用的[[IO 多路复用技术]]主要有：`select`、`epoll`、`evport`和`kqueue`等。每个 IO 多路复用函数库在 Redis 源码中都对应一个单独的文件，比如`ae_select.c`，`ae_epoll.c`， `ae_kqueue.c`等
			- Redis 会根据不同的操作系统，按照不同的优先级选择多路复用技术
			- 事件响应框架一般也采用该架构，比如 netty 和 libevent
			  ![](https://pdai.tech/images/db/redis/db-redis-event-2.png){:height 223, :width 452}
		- 文件事件处理器
			- 如下图，文件事件处理器由四个部分组成，分别是套接字、I/O 多路复用程序、文件事件分派器、事件处理器
			  ![](https://pdai.tech/images/db/redis/db-redis-event-3.png){:height 305, :width 452}
			- 文件事件是对[[套接字]]操作的抽象，每当一个套接字准备好执行 `accept`、`read`、`write`和 `close` 等操作时，就会产生一个文件事件。因为 Redis 通常会连接多个套接字，所以多个文件事件有可能并发的出现
			- I/O 多路复用程序负责监听多个套接字，并向文件事件派发器传递那些产生了事件的套接字
			- 尽管多个文件事件可能会并发地出现，但 I/O 多路复用程序总是会将所有产生的套接字都放到同一个队列(也就是后文中描述的 aeEventLoop 的 fired 就绪事件表)里边，然后文件事件处理器会以有序、同步、单个套接字的方式处理该队列中的套接字，也就是处理就绪的文件事件
			  ![](https://pdai.tech/images/db/redis/db-redis-event-4.png)
- How Good
- Refs
- See Also