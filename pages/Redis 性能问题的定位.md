- What
- Why
- How
	- TODO 看延迟 60 秒内的最大响应延迟
		- ```sh
		  ~ redis-cli -h 127.0.0.1 -p 6379 --intrinsic-latency 60
		  Max latency so far: 4 microseconds.
		  Max latency so far: 5 microseconds.
		  Max latency so far: 9 microseconds.
		  Max latency so far: 16 microseconds.
		  Max latency so far: 23 microseconds.
		  Max latency so far: 40 microseconds.
		  Max latency so far: 42 microseconds.
		  Max latency so far: 44 microseconds.
		  Max latency so far: 76 microseconds.
		  Max latency so far: 136 microseconds.
		  Max latency so far: 234 microseconds.
		  
		  13263167 total runs (avg latency: 4.5238 microseconds / 4523.81 nanoseconds per run).
		  Worst run took 2066x longer than the average latency.
		  ```
	- 慢日志（slowlog）
		- 慢查询，导致后边的请求排队；对于客户端来说，响应时间也会变长
		  ![](https://pdai.tech/images/db/redis/redis-performance-2.jpeg)
	- bigkey
		- TODO 大对象
	- 集中过期
		- 两种规避方案
			- 集中过期 key 增加一个随机过期时间，把集中过期的时间打散，降低 Redis 清理过期 key 的压力
			- Redis 4.0 以上版本，可以开启`lazy-free`，当删除过期 key 时，把释放内存的操作放到后台线程中执行，避免阻塞主线程
	- fork 耗时严重
		- 主进程创建子进程时，会调用操作系统提供的 fork 函数，会造成主进程阻塞
	- 使用 swap
		- 当内存中的数据被换到磁盘上后，Redis 再访问这些数据时，就需要从磁盘上读取，访问磁盘的速度要比访问内存慢几百倍
	- 内存对象
		- Redis 4.0 版本提供了自动碎片整理功能，可以通过配置开启
- How Good
- Refs
- See Also