- What
- Why
	- 大数据量和高吞吐量的数据库应用会对单机的性能造成较大压力, 大的查询量会将单机的CPU耗尽, 大的数据量对单机的存储压力较大, 最终会耗尽系统的内存而将压力转移到磁盘IO上
	- 为了上述问题，有两个基本方法
		- 垂直拓展：增加更多的 CPU 和存储资源来扩展容量
		- 水平拓展：将数据集分布在更多服务器上。**MongoDB 分片就是水平扩展的体现**
- How
	- 设计思想
		- 分片为应对大数据量和高吞吐量提供了方法。使用分片减少了每个分片需要处理的请求数，因此，通过水平扩展，集群可以提高自己的存储量和吞吐量。例如，当插入一条数据时，应用只需要访问这条数据的分片
	- 目的
		- 读/写能力提升
		- 存储容量扩容
		- 高可用性
	- 分片集群的结构
	  collapsed:: true
		- 包含以下组件
			- `shard`：即分片，真正的数据存储位置，以 chunk 为单位存储；每个分片可以部署为一个复制集
			- `mongos`：查询的路由，提供客户端和分片集群之间的接口
			- `config servers`：存储元数据和配置数据
			   ![](https://pdai.tech/images/db/mongo/mongo-z-shard-1.png){:height 347, :width 478}
			  这里要注意 mongos 提供的是客户端 application 和 MongoDB 分片集群的路由功能，这里分片集群包含了分片的 collection 和非分片的 collection。下图展示了通过路由访问分片的 collection 和非分片的 collection
			  ![](https://pdai.tech/images/db/mongo/mongo-z-shard-2.png)
			-
	- 内部如何管理数据
		- 在一个 shard server 内部，MongoDB 会把数据分为chunks，每个 chunk 代表这个 shard server 内部一部分数据
			- chunk 的两个用途
			  collapsed:: true
				- `Splitting`
					- 当一个 chunk 的大小超过配置中的 chunk size 时，MongoDB 的后台进程会把这个 chunk 切分成更小的 chunk，从而避免 chunk 过大的情况
				- `Balancing`
					- 在 MongoDB 中，balancer 是一个后台进程，负责 chunk 的迁移来均衡各个 shard server 的负载
					- 系统初始一个 chunk，chunk size 默认值 64M，生产库上选择适合业务的 chunk size 是最好的
					- MongoDB 会自动拆分和迁移 chunks
		- 分布集群的数据分布
			- 使用 chunk 来存储数据
			- 集群搭建完成之后，默认开启了一个 chunk，大小是 64M
			- 存储需求超过 64M，chunk 会进行分裂（如果单位时间存储需求很大，需设置更大的 chunk）
			- chunk 会被自动均衡迁移
		- chunk 的分裂和迁移
		  collapsed:: true
			- 随着数据的增长，当大小超过了配置的 chunk size（默认是 64M），该 chunk 就会分裂成两个。数据的增长会让 chunk 分裂得越来越多
			  ![](https://pdai.tech/images/db/mongo/mongo-z-shard-4.png){:height 188, :width 250}
			- 分裂后，各 shard server 的 chunk 数量可能会不平衡；此时 balancer 就会执行自动平衡，把 chunk 从 chunk 数量最多的 shard 节点挪动到数量最少的节点
			  ![](https://pdai.tech/images/db/mongo/mongo-z-shard-5.png){:height 180, :width 478}
		- Collection 的分片
			- 采用**分片键（shard key）**进行分片
			- 采用的算法
				- **哈希分片（hash sharding）**
					- 利用哈希索引作为分片键，其最大好处是保证数据在各个节点分布基本均匀
					- MongoDB 计算一个字段的哈希值，并用这个哈希值来创建数据块。在使用基于哈希分片的系统中，拥有**相近分片键**的文档很可能不会存储在同一个数据块中，因此数据的分离性更好一些
					  ![](https://pdai.tech/images/db/mongo/mongo-z-shard-6.png)
				- **范围分片（range sharding）**
					- 将单个 Collection 的数据分散存储在多个 shard 上，用户可以指定集合内文档的某个字段即 shard key 来进行范围拨片（range sharding）
- How Good
- Refs
- See Also