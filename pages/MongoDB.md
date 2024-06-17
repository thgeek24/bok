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
		- 插件式存储引擎架构
			- 实现了 Server 层和存储引擎层的解耦，可以支持多种存储引擎，如 MySQL 既可以支持 B-Tree 结构的 InnoDB 存储引擎，也可以支持[[LSM 结构]]的 [[RocksDB 存储引擎]]
		- B-Tree + Page
			- ![](https://pdai.tech/images/db/mongo/mongo-y-ds-3.jpg)
			- 上图是 WiredTiger 在内存的大概布局图，
- How Good
- Refs
- See Also
-