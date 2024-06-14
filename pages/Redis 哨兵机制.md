- What
	- 核心功能
		- 主节点的自动故障转移
	- 功能详述
		- 监控（Monitoring）
			- 哨兵会不断检查主节点和从节点是否运行正常
		- 自动故障转移（Automatic failover）
			- 当主节点不能正常工作时，哨兵会开始自动故障转移操作，它会将失效主节点的其中一个从节点升级为新的主节点，并让其他从节点改为复制新的主节点
		- 配置提供者（Configuration provider）
			- 客户端在初始化时，通过连接哨兵来获得当前Redis服务的主节点地址
		- 通知（Notification）
			- 哨兵可以将故障转移的结果发送给客户端
		- (其中，监控和自动故障转移功能，使得哨兵可以及时发现故障并转移主节点；而配置提供者和通知功能，则需要与客户端交互才能体现)
	- 逻辑图
		- ![](https://pdai.tech/images/db/redis/db-redis-sen-1.png)
- Why
- How
	- 哨兵集群的组建方式
		- Redis 提供的 pub/sub 机制，即发布/订阅机制，使得哨兵实例之间可以相互发现
			- 在主从集群中，主库上有一个名为`__sentinel__:hello`的频道，不同哨兵就是通过它来相互发现，实现相互通信的；如下图，哨兵 1 把自己的 IP (172.16.19.3)和端口（26579）发布到`__sentinel__:hello`频道，哨兵 2 和 3 订阅了该频道，于是哨兵 2 和 3 就可以从这个频道获得到哨兵 1 的 IP 和端口，然后，哨兵 2 和 3 就可以和哨兵 1 建立网络连接
			  ![](https://pdai.tech/images/db/redis/db-redis-sen-6.jpg)
			  同样的方式，哨兵 2 和 哨兵 3 也可以建立网络连接，这样便形成了哨兵集群。它们相互间可以通过网络连接进行通信，比如说对主库有没有下线这件事进行判断和协商
	- Redis 哨兵是如何监控 Redis 集群的
		- 通过哨兵向主库发送`INFO`命令完成
		- 如下图，哨兵 2 给主库发送 INFO 命令，主库接受到这个命令后，就会把从库列表返回给哨兵
		- 哨兵 2 根据从库列表中的连接信息，和每个从库建立连接，并在这个连接上持续地对从库进行监控
		- 哨兵 1 和 3 可以用同样的方法和从库建立连接
		- ![](https://pdai.tech/images/db/redis/db-redis-sen-7.jpg){:height 262, :width 452}
	- Redis 哨兵判断主库下线
	  collapsed:: true
		- **主观下线**：任何一个哨兵都可以监控探测，并做出 Redis 节点下线的判断
		- **客观下线**：由哨兵集群共同决定 Redis 节点是否下线
		  ---
		- 当某个哨兵（如下图所示的哨兵 2）判断主库“主观下线”后，向哨兵 2 和 3 发送`is-master-down-by-addr`命令
		- 其他哨兵会根据自己和主库的连接情况，做出 Y 或 N 的响应，Y 相当于赞成票，N 相当于反对票
		- 如果赞成票数（这里是 2）大于或等于哨兵配置文件中的`quorum`参数（比如这里是 quorum=2），则可以判定主库已客观下线了
		- ![](https://pdai.tech/images/db/redis/db-redis-sen-2.jpg)
	- [[Redis 哨兵的选举机制]]
	- 5 个哨兵（1 主 4 从），quorum 为 2，如果 3 个哨兵故障，当主库宕机时，哨兵能否判断主库“客观下线”？能否自动切换？
	  collapsed:: true
		- 可以判断“客观下线”：当一个哨兵判断主库“主观下线”后，询问另外一个哨兵后也会得到同样的结果，2 个哨兵都判定“主观下线”，达到了 quorum 的值，因此，**哨兵集群可以判定主库为“客观下线”**
		- 哨兵不能完成主从切换：哨兵标记主库“客观下线后”，在选举“哨兵领导者”时，一个哨兵必须拿到超过多数的选票(5/2+1=3票)。但目前只有2个哨兵活着，无论怎么投票，一个哨兵最多只能拿到2票，永远无法达到`N/2+1`选票的结果
	- 主库被判定客观下线后，如何从剩余的从库中选出新的主库？
	  collapsed:: true
		- 过滤掉不健康的（下线或断线），没有回复过哨兵 ping 响应的从节点
		- 选择`slave-priority`从节点优先级最高（redis.conf）的
		- 选择复制偏移量最大，只复制最完成的从节点
		  ![](https://pdai.tech/images/db/redis/db-redis-sen-3.jpg)
	- 新的主库选出后，如何进行故障转移？
		- 初始：判断主库客观下线了，同时选出`sentinel 3`为哨兵 leader
		  ![](https://pdai.tech/images/db/redis/db-redis-sen-1.png){:height 338, :width 452}
		- 故障转移流程：
			- 将 slave-1 脱离原从节点（PS: 下图步骤 1 中应该是`replicaof no one`)，升级为主节点
			- 将从节点 slave-2 指向新的主节点
			- 通知客户端主节点已更换
			- 将原主节点变为从节点
			- 将原主节点（oldMaster）变成从节点，指向新的主节点
			  ![](https://pdai.tech/images/db/redis/db-redis-sen-4.png)
		- 转移后
		  ![](https://pdai.tech/images/db/redis/db-redis-sen-5.png)
		-
- How Good
- Refs
- See Also