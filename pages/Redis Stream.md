- What
	-
- Why
- How
	- 基于Redis做[[消息队列]]，其实现方式有多种
		- **PUB/SUB**，订阅/发布模式
			- 无法持久化，如果出现网络断开、Redis宕机等，消息就会被丢弃；
		- 基于 **List LPUSH+BRPOP** 或者 基于 **Sorted-Set** 的实现
			- 支持持久化，但不支持多播，分组消费等
	- 消费组消费图
	  ![](https://pdai.tech/images/db/redis/db-redis-stream-3.png)
	- 使用场景
		- 即时通信、大数据分析、异地数据备份等
		  ![](https://pdai.tech/images/db/redis/db-redis-stream-4.png){:height 162, :width 452}
		- 客户端可以平滑扩展，提高处理能力
		  ![](https://pdai.tech/images/db/redis/db-redis-stream-5.png){:height 216, :width 452}
		- [[Redis命令]]
			- `XADD memberMessages * msg one`
				- `XADD`表明要向一个特定的stream添加一个entry
				- `memberMessages`是上述stream的名称
				- `*`表明Redis要为这个新加入的entry生成唯一ID；该ID由时间戳和序号构成，中间用破折号连接；其中时间戳和序号都是64位整型
				- `msg`是key
				- `one`是value
			- `XINFO`
			- `XPENDING`
			  id:: 66599778-950b-40e0-9371-4f9e49d1f56f
				- 检查stream里已被读取，但未被消费者确认处理完毕的消息
			- `XACK`
			  id:: 665aa9ba-8741-435a-bbab-906e79693845
				- 告知消息处理完成
			- `XCLAIM`
				- 声明某个pending的消息不能被原consumer确认，且转交给其他consumer消费
			- `XREADGROUP`
			- `XDEL`
				- 删除某条消息
	- 应对[[时间回拨]]
		- Redis的stream有一个`last_generated_id`属性，当系统时间发生改变，比`last_generated_id`中记录的时间戳还早，那么生成的ID，就会沿用前文所述的时间戳，序号递增
	- 应对消费者崩溃
		- 为了解决组内消息读取但处理期间消费者崩溃带来的消息丢失问题，STREAM设计了pending列表，用来记录已经读取但未确认处理完毕的消息
		  {{embed ((66599778-950b-40e0-9371-4f9e49d1f56f))}}
		  ```
		  XPENDING myStream myGroup # myGroup的Pending情况
		  XPENDING myStream myGroup - + 10 # myGroup的Pending情况(start end count，前10条记录)
		  XPENDING myStream myGroup - + 10 consumerA # myGroup的consumerA的Pending情况(前10条记录)
		  ```
		- 每个PENDING的消息有4个属性
			- 消息ID
			  logseq.order-list-type:: number
			- 所属消费者
			  logseq.order-list-type:: number
			- IDLE，已读取时长
			  logseq.order-list-type:: number
			- delivery counter，消息被读取次数（同一条消息被反复转移给各个消费者，会累加）
			  logseq.order-list-type:: number
		- {{embed ((665aa9ba-8741-435a-bbab-906e79693845))}}
	- 坏消息问题/死信问题
		- 当某条消息的deliver count达到预设的临界值，我们就认为它是坏消息（死信，DeadLetter，无法投递的消息）
		- TODO 可以使用`XDEL`命令将其删除
		  ```
		  XDEL myStream 1717219254074-0
		  ```
		  但这不会删除pending中的消息（比如这里的1717219254074-0，通过XPENDING命令仍可见），可以执行XACK标识其处理完毕
- How Good
- Refs
- See Also