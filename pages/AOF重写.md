- What
	- 创建一个新的AOF文件来替换现有的AOF，新旧两个AOF文件保存的数据相同，但新AOF文件没有了冗余命令
	  ![](https://pdai.tech/images/db/redis/redis-x-aof-1.jpg)
- Why
- How
	- 会阻塞吗？
		- 主进程fork出后台的bgrewriteaof子进程，同时把主进程的内存拷贝一份给bgrewriteaof子进程，这里面就包含了数据库的最新数据。然后，bgrewriteaof子进程就可以在不影响主线程的情况下，逐一把拷贝的数据写成操作，记入重写日志
			- 在fork出子进程时，会短暂的阻塞主进程
			- 在重写时，由于是子进程进行操作的，所以不会阻塞主进程
	- 重写时机
		- 两个配置项：
			- `auto-aof-rewrite-min-size`: 表示触发AOF重写时文件的最小大小，默认64MB
			- `auto-aof-rewrite-percentage`: 表示当前AOF文件比上一次重写后AOF文件的增量大小，和上一次重写AOF文件大小的比值
	- 重写时有新数据写入，该如何处理？
		- 总结：一个拷贝，两处日志
		- 在fork出子进程时的拷贝，以及在重写时，如果有新数据写入，主线程就会将命令记录到两个aof日志内存缓冲区中。如果AOF写回策略配置的是always，则直接将命令写回旧的日志文件，并且保存一份命令至AOF重写缓冲区，这些操作对新的日志文件是不存在影响的。（旧的日志文件：主线程使用的日志文件，新的日志文件：bgrewriteaof进程使用的日志文件）
		- 在bgrewriteaof子进程完成会日志文件的重写操作后，会提示主进程已经完成重写操作，主进程会将AOF重写缓冲中的命令追加到新的日志文件后面。这时候在高并发的情况下，AOF重写缓冲区积累可能会很大，这样就会造成阻塞，Redis后来通过Linux管道技术让AOF重写期间就能同时进行回放，这样AOF重写结束后只需回放少量剩余的数据即可
		- 最后通过修改文件名的方式，保证文件切换的原子性
		- 在AOF重写日志期间发生宕机的话，因为日志文件还没切换，所以恢复数据时，用的还是旧的日志文件
	- 主线程fork出子进程的是如何复制内存数据的？
		- 子进程时会拷贝父进程的页表，即虚实映射关系（虚拟内存和物理内存的映射索引表），而不会拷贝物理内存。这个拷贝会消耗大量cpu资源，并且拷贝完成前会阻塞主线程，阻塞时间取决于内存中的数据量，数据量越大，则内存页表越大。拷贝完成后，父子进程使用相同的内存地址空间
		- 但主进程是可以有数据写入的，这时候就会拷贝物理内存中的数据。如下图（进程1看做是主进程，进程2看做是子进程）：
		  ![](https://pdai.tech/images/db/redis/redis-x-aof-3.png)
		  在主进程有数据写入时，而这个数据刚好在页c中，操作系统会创建这个页面的副本（页c的副本），即拷贝当前页的物理数据，将其映射到主进程中，而子进程还是使用原来的的页C
	- 在重写日志整个过程时，主线程有哪些地方会被阻塞
		- fork子进程时，需要拷贝虚拟页表，会对主线程阻塞
		- 主进程有bigkey写入时，操作系统会创建页面的副本，并拷贝原有的数据，会对主线程阻塞
		- 子进程重写日志完成后，主进程追加aof重写缓冲区时可能会对主线程阻塞
	- 为什么AOF重写不复用原AOF文件
		- 父子进程写同一个文件会产生竞争问题，影响父进程的性能
		- 如果AOF重写过程中失败了，相当于污染了原本的AOF文件，无法做恢复数据使用
- How Good
- Refs
- See Also