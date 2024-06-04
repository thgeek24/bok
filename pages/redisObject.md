- What
	- Redis 内部使用的数据结构，用于表示 Redis 中的各种数据类型，如字符串、列表、哈希、集合和有序集合等。它是 Redis 对不同类型数据的抽象表示，提供了统一的接口和操作方式
	- 定义（C语言）
	  ```c
	  /*
	   * Redis 对象
	   */
	  typedef struct redisObject {
	  
	      // 类型
	      unsigned type:4;
	  
	      // 编码方式
	      unsigned encoding:4;
	  
	      // LRU - 24位, 记录最末一次访问时间（相对于lru_clock）; 或者 LFU（最少使用的数据：8位频率，16位访问时间）
	      unsigned lru:LRU_BITS; // LRU_BITS: 24
	  
	      // 引用计数
	      int refcount;
	  
	      // 指向底层数据结构实例
	      void *ptr;
	  
	  } robj;
	  ```
	- 图示
	  ![](https://pdai.tech/images/db/redis/db-redis-object-1.png)
	-
- Why
- How
- How Good
- Refs
- See Also