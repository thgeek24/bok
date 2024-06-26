- What
	- 一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中
	- 总结
		- Redis事务就是一次、顺序性、排他性的执行一个队列中的一系列命令
- Why
- How
	- 相关命令 #card
	  collapsed:: true
		- `MULTI`
			- 开启事务，Redis会将后续的命令逐个放入队列中，然后使用`EXEC`命令来原子化执行这个命令系列
		- `EXEC`
			- 执行事务中的所有操作命令
		- `DISCARD`
			- 取消事务，放弃执行事务块中的所有命令
		- `WATCH`
			- 监视一个或多下key，如果事务在执行前，这个key（或多个key）被其他命令修改，则事务被中断，不会执行事务中的任何命令
		- `UNWATCH`
			- 取消`WATCH`对所有key的监视
	- 三个阶段
	  collapsed:: true
		- 开启：以`MULTI`开始一个事务
		- 入队：将多个命令入队到事务中，接到这些命令并不会立即执行，而是放到等待执行的事务队列里面
		- 执行：由`EXEC`命令触发事务
		  
		  当一个客户端切换到事务状态之后，服务器会根据这个客户端发来的不同命令执行不同的操作：
		- 如果客户端发来的是`EXEC`、`DISCARD`、`WATCH`、`MULTI`命令中的其中一个，那么服务器立即执行这个命令
		- 如果客户端发来的是上述四个命令之外的命令，那么服务器并不立即执行这个命令，而是将这个命令放到一个事务队列里，然后向客户端发送`QUEUED`回复
		  ![](https://pdai.tech/images/db/redis/db-redis-trans-1.png)
	- 出现错误时的处理
	  collapsed:: true
		- 语法错误（编译器错误）
		  在开启事务后，修改k1值，但k2语法错误，最终导致事务提交失败，k1、k2保留原值
		  ```sh
		  > set k1 v1
		  "OK"
		  > set k2 v2
		  "OK"
		  > multi
		  "OK"
		  > set k1 11
		  "QUEUED"
		  > sets k2 22
		  "ERR unknown command 'sets', with args beginning with: 'k2' '22' "
		  > exec
		  "EXECABORT Transaction discarded because of previous errors."
		  > get k1
		  "v1"
		  > get k2
		  "v2"
		  ```
		- 类型错误（运行时错误）
		  在开启事务后，修改k1值为11，k2值为22，但将k2的类型作为List，在运行时检测类型错误，最终导致事务提交失败，此时事务并没有回滚，而是跳过错误命令继续执行，结果k1值改变、k2保留原值
		  ```sh
		  > set k1 v1
		  "OK"
		  > set k2 v2
		  "OK"
		  > multi
		  "OK"
		  > set k1 11
		  "QUEUED"
		  > lpush k2 22
		  "QUEUED"
		  > exec
		  1) "OK"
		  2) "ReplyError: WRONGTYPE Operation against a key holding the wrong kind of value"
		  > get k1
		  "11"
		  > get k2
		  "v2"
		  ```
	- WATCH是如何实现监视的
	  collapsed:: true
		- Redis使用`WATCH`命令来决定是继续执行还是回滚，那就需要在`MULTI`之前使用`WATCH`来监控某些键值对，然后使用`MULTI`命令来开启事务，此后对数据结构操作的各种命令进入队列
		- 当使用`EXEC`执行事务时，首先会比对`WATCH`中所监控的键值对，如果没有发生改变，它会执行事务队列中的命令，提交事务；如果发生改变，将不会执行事务中的任何命令，同时事务回滚
		- TODO 无论事务是执行还是回滚，事务结束时，Redis都会取消开启事务前的`WATCH`命令
		- ![](https://pdai.tech/images/db/redis/db-redis-trans-2.png){:height 325, :width 452}
	- 不支持回滚
	  collapsed:: true
		- Redis命令只会因为错误的语法而失败（这种问题不能在入队的时候发现），或者命令用在了类型错误的键上；这些失败的命令是由编程错误造成的，它们应该在开发的过程中被发现，而不应该出现在生产环境中
		- 因为不需要对回滚进行支持，Redis的内部内部可以保持简单而快速
		  有观点认为 Redis 处理事务的做法会产生 bug ， 然而在通常情况下， **回滚并不能解决编程错误带来的问题**。例如，本想通过 INCR 命令将键的值加上 1 ， 却不小心加上了 2 ， 又或者对错误类型的键执行了 INCR ， 回滚是没有办法处理这些情况的
	- 对ACID的支持性理解
	  collapsed:: true
		- 原子性atomicity
			- 通过前文可知，Redis事务运行期的错误是不会回滚的，很多文章因此说Redis事务违背原子性的
			- Redis在官方文档中认为，**Redis的事务是原子性的：所有的命令，要么全部执行，要么全部不执行**。而不是完全成功
		- 一致性consistency
			- Redis事务可以保证命令失败的情况下回滚，即数据恢复到没有执行前的样子，是保证一致性的，除非Redis进程意外终结
		- **隔离性Isolation**
			- Redis事务严格遵守隔离性，原因是Redis是单进程单线程模式(v6.0之前），可以保证命令执行过程中不会被其他客户端命令打断
			- 但是，Redis不像其它结构化数据库有隔离级别这种设计
		- **持久性Durability**
			- Redis事务不保证持久性，这是因为Redis持久化策略中的RDB和AOF都是异步执行的，不保证持久性是出于对性能的考虑
	- 其它实现
	  collapsed:: true
		- 基于Lua脚本，Redis可以保证脚本内的命令一次性、按顺序地执行，也不提供事务运行错误的回滚，执行过程中如果部分命令运行错误，剩下的命令还是会继续运行完
		- 基于中间标记变量，通过另外的标记变量来确认事务是否执行完成，读取数据时先读取该标记变量是否完成。但这样会需要额外写代码实现，比较繁琐
- How Good
- Refs
- See Also