- What
	- Replica set：一组复制集就是一组 MongoDB 实例掌管同一个数据集，实例可以在不同的机器上面。实例中包含一个主导，接收客户端所有的写入操作；其他都是副本实例，它们从主服务器上获取数据并保持同步
	  ![](https://pdai.tech/images/db/mongo/mongo-z-rep-1.png){:height 447, :width 537}
	- 基本成员
		- 主节点（Primary）
			- 包含了所有写操作的日志
			- 副本服务器集群包含所有的主服务器数据，因此当主服务器挂掉了，就会在副本服务器集群中，重新选择一个服务器作为主服务器
		- 从节点（Secondary）
			- 正常情况下，复制集的 Secondary 会参与 Primary 选举（自身也有可能被选为 Primary），并从 Primary 同步更新写入的数据，以保证与 Primary 存储相同的数据；增加 Secondary 节点可以提供复制集的读服务能力，同时提升复制集的可用性
			- 对从节点的细分
				- **Priority0** 选举优先级为 0，即不会被选举为 Primary
				- **Hidden** 不会收到来自应用程序的请求，可使用 Hidden 节点做一些数据备份、离线计算的任务，不会影响复制集的服务
					- **Delayed** 属于 Hidden 节点，数据落后于 Primary 节点一段时间（可配置，比如一个小时）；当错误或者无效的数据写入 Primary 时，可通过 Delayed 的数据恢复到之前的时间点
		- 仲裁节点（Arbiter）
			- 只参与投票，不能被选为 Primary，并且不从 Primary 同步数据。比如部署一个只有两个节点的复制集，一个 Primary，一个 Secondary，此时任意节点宕机，复制集将不能提供服务了（无法选出 Primary）；如果给该复制集添加一个 Arbiter 节点，即使有节点宕机，仍能选出 Primary
- TODO Why (原文质量过差)
  :LOGBOOK:
  CLOCK: [2024-06-18 Tue 14:57:30]--[2024-06-18 Tue 14:57:33] =>  00:00:03
  :END:
	- 保证数据的冗余性和可靠性，通过在不同的机器上保存副本来保证数据不会因为单点损坏而丢失，能够随时应对数据丢失、机器损坏带来的风险
	- 提高读取能力（读取服务器和写入服务器在不同的地方），提高整个系统的负载（不同的服务器为不同的用户服务）
- How
	- 常见部署架构
	  collapsed:: true
		- 三节点的单数据中心
			- 一主两从
				- 一个主节点
				- 两个从节点（主节点宕机时，这两个从节点都可以被选为主节点）
				  ![](https://pdai.tech/images/db/mongo/mongo-z-rep-5.png)
				  当主节点宕机后，两个从节点都会进行竞选，其中一个变为主节点；当原节点恢复后，作为从节点加入当前复制集即可
				  ![](https://pdai.tech/images/db/mongo/mongo-z-rep-6.png)
			- 一主一从一仲裁
				- 一个主节点
				- 一个从节点，可以在选取中成为主节点
				- 一个仲裁节点，在选举中只能进行投票，不能成为主节点
				  ![](https://pdai.tech/images/db/mongo/mongo-z-rep-7.png){:height 178, :width 449}
				  当主节点宕机时，将会选择从节点成为主节点；当原主节点恢复后，作为从节点加入当前的复制集群即可
				  ![](https://pdai.tech/images/db/mongo/mongo-z-rep-8.png)
		- 五节点的多数据中心
			- 两个数据中心：数据中心 1 的三个成员和数据中心 2 的两个成员
				- 如果数据中心 1 发生故障，则复制集将变为只读
				- 如果数据中心 2 发生故障，则复制集将保持可写状态，因为数据中心 1 的成员可以创建多数
			- 三个数据中心：两个成员是数据中心 1，两个成员是数据中心 2，一个成员是站点数据中心 3
				- 如果任何数据中心发生故障，复制集将保持可写状态，因为其余成员可以举行选举
				- 例如，以下 5 个成员的复制集，将其成员分布在三个数据中心中
				  ![](https://pdai.tech/images/db/mongo/mongo-z-rep-9.png)
	- 数据高可用的保障措施
	  collapsed:: true
		- 选举机制
			- 假设复制集内能够投票的成员数量为 N，则大多数为 N/2+1
			- 当复制集内存活成员数量不足大多数时，整个复制集将无法选举出 Primary，复制集将无法提供写服务，出于只读状态
			- 举例
				- 3 个投票节点需要 2 个节点的赞成票，容忍选举失败次数为 1
				- 5 个投票节点需要 3 个节点的赞成票，容忍选举失败次数为 2
				  (通常投票节点为奇数，这样可以减少选举失败的概率)
			- 触发选举机制的情况
				- 往复制集中新增节点
				- 初始化复制集时
				- 对复制集进行维护时，比如`rs.stepDown()`或者`rs.reconfig()`操作时
				- 节点失联，比如超时（默认 10 秒）
		- 故障转移期间的回滚
			- TODO 当成员在故障转移后重新加入其复制集时，回滚将还原以前的主数据库上的写操作。本质上就是保持数据的一致性
			- TODO 仅当主服务器接受了在主服务器降级之前，辅助服务器未成功复制的写操作时，才需要回滚。当主数据库作为辅助数据库重新加入集合时，它会还原或“回滚”其写入操作，以保持数据库与其他成员的一致性
	- 同步数据
	  collapsed:: true
		- 目的
			- 维护共享数据集的最新副本，包括复制集的辅助成员或复制其他成员的数据
		- 两种形式
			- **初始同步（Initial Sync）**使用完整的数据集填充新成员，即**全量同步**
				- 新节点加入，无任何 oplog，此时需先进行 initial sync
				- initial sync 开始时，会主动将 _initialSyncFlag 字段设置为 true，正常结束后再设置为 false；如果节点重启时，发现 _initialSyncFlag 为 true，说明上次全量同步失败了，则会重新新进行 initial sync
				- 当用户发送 resync 命令时，initialSyncRequested 会设置为 true，此时会重新开始以此 initial sync
			- **复制（Replication）**将正在进行的更改应用于整个数据集，即**增量同步**
				- initial sync 结束后，Secondary 就会“不断拉去主节点上心产生的 oplog 并重放”
- How Good
- Refs
- See Also