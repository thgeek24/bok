- What
	- 定义
		- MongoDB 是面向文档的 NoSQL 数据库，用于大量数据存储
	- Release Date of First Version
		- Aug. 2009
	- MongoDB VS RDBMS
		- |SQL 术语/概念|MongoDB 术语/概念|解释/说明|
		  |database|database|数据库|
		  |table|collection|数据库表/集合|
		  |row|document|数据记录行/文档|
		  |column|field|数据字段/域|
		  |index|index|索引|
		  |table joins||表连接，MongoDB 不支持|
		  |primary key|primary key|主键，MongoDB 自动将 _id 设置为主键|
		- ![](https://pdai.tech/images/db/mongo/mongo-y-arch-2.png)
- Why
- How
	- 聚合
		- 管道方式
			- Aggregation Pipeline：类似于将 SQL 中的 group by + order by + left join ... 等操作管道化。MongoDB 的聚合管道（Pipeline）将 MongoDB 文档在一个阶段（Stage）处理完毕后将结果传递给下一个阶段（Stage）处理。阶段（Stage）操作是可以重复的
			- ![](https://pdai.tech/images/db/mongo/mongo-x-usage-11.png){:height 352, :width 478}
		- Map Reduce 方式
			- ![](https://pdai.tech/images/db/mongo/mongo-x-usage-12.png)
	- Spring Data 和 MongoDB 的集成
		- 引入`mongodb-driver`，使用最原生的方式通过 Java 调用 mongodb 提供的 Java driver
		- 引入`spring-data-mongo`，自行配置使用 spring data 提供的对 MongoDB 的封装
			- 使用 MongoTemplate 方式
			- 使用 MongoRepository 方式
		- 引入`spring-data-mongo-starter`，采用 Spring autoconfig 机制自动装配，然后再使用 MongoTemplate 或者 MongoRepository 方式
	- 存储引擎
		- WiredTiger（v3.2  之后默认采用）
		- MMAPV1（v3.2 之前采用，v4.2 移除）
		- In Memory
	- WT存储引擎（WiredTiger）
	  collapsed:: true
		- 插件式存储引擎架构
			- 实现了 Server 层和存储引擎层的解耦，可以支持多种存储引擎，如 MySQL 既可以支持 B-Tree 结构的 InnoDB 存储引擎，也可以支持[[LSM 结构]]的 [[RocksDB 存储引擎]]
		- B-Tree + Page
		  collapsed:: true
			- ![](https://pdai.tech/images/db/mongo/mongo-y-ds-3.jpg)
			  上图是 WiredTiger 在内存的大概布局图，通过它我们可梳理清楚存储引擎是如何将数据加载到内存，然后如何通过相应数据结构来支持查询、插入、修改操作的
			- 内存里边的 B-Tree 包含三中类型的 page，即 root page，internal page 和 leaf page，前两者包含指向其子页的 page index 指针，不包含集合中的真正数据；后者包含集合中的真正数据即 keys/values 和指向其父页的 home 指针
				- Page
					- 数据以 page 为单位加载到 cache 上，cache 里边又会生成不同类型的 page 以及为不同类型的 page 分配不同大小的内存， eviction 触发机制和 reconcile 动作都是发生在 page 上，page 大小持续增加时会被分割成多个小 page，所有这些操作都是我围绕一个 page 完成的
					- 典型的生命周期
					  ![](https://pdai.tech/images/db/mongo/mongo-y-page-1.png){:height 211, :width 390}
				- CheckPoint
					- 相当于一个日志，记录了上次 CheckPoint 后相关数据文件的变化
					- 作用
						- 将内存里边发生修改的数据写到数据文件进行持久化保存，确保数据一致性
						- 实现数据库在某个时刻意外发生故障，再次启动时，缩短数据库的恢复时间。WiredTiger 存储疫情中的CheckPoint 模块就是来实现这个功能的
					- 关键信息
						- ![](https://pdai.tech/images/db/mongo/mongo-x-checkpoint-1.png)
						- 每个 checkpoint 包含一个 root page、三个列表（指向磁盘上 pages 的具体位置）以及磁盘上文件的大小
		- WT 事务机制
			- 使用了三个技术
				- snapshot（事务快照）
				- MVCC（多版本并发控制）
				- redo log（重做日志）
			- 为了实现这三个技术，定义了一个基于这三个技术的事务对象和**全局事务管理器**
		- WT 缓存淘汰
			- eviction cache 是一个 LRU cache，即页面置换算法缓冲区，它对数据页采用的是**分段局部扫描**和**淘汰**，而不是对内存中所有的数据页做全局管理
			- 基本思路
				- 一个线程阶段性的去扫描各个 B-Tree，并把 B-Tree 可以进行淘汰的数据页添加到一个 LRU queue 中
				- 当 queue 填满后记录下这个过程中当前的 B-Tree 对象和 B-Tree 的位置（这个位置将作为下次阶段性扫描的位置）
				- 对 queue 中的数据页按照访问热度排序，然后各个淘汰线程按照淘汰优先级淘汰 queue 中的数据页
				- 然后周期性重复如上过程
			- evict 过程涉及到多个 eviction thread 和 hazard pointer 技术
			- evict 过程以 page 为单位做淘汰，而不是以 K/V。这一点和 [[memcached]] 、Redis 等常用的缓存 LRU 不太一样，因为在磁盘上数据的最小描述单位是 page block，而不是记录
	- [[MongoDB 复制集]]
	- [[MongoDB 分片]]
	- [[MongoDB 备份恢复]]
	- [[MongoDB 设计文档模型]]
	-
- How Good
- Refs
- See Also