- What
	- 新建单个文档的步骤
	  ![](https://pdai.tech/images/db/es/es-th-2-4.png)
		- 客户端向 Node 1 发送新建、索引或删除请求
		- 节点使用文档的 _id 确定文档属于分片 0。请求会被转发到 Node 3，因为分片 0 的主分片目前被分配在 Node 3 上
		- Node 3 在主分片上面执行请求。如果成功了，它将请求并行转发到 Node 1 和 Node 2 的副本分片上。一旦所有的副本分片都报告成功，Node 3 将向协调节点报告成功，协调节点向客户端报告成功
	- 索引流程
	  id:: 6674ee77-755c-4dfb-8207-1f55c77fdfa9
	  ![](https://pdai.tech/images/db/es/es-th-2-5.jpeg){:height 195, :width 482}
		- 协调节点默认使用文档 ID 参与计算（也支持通知 routing），以便为路由提供合适的分片
		  ```sh
		  shard = hash(document_id) % (num_of_primary_shards)
		  ```
		- 当分片所在的节点接收到来自协调节点的请求后，会将请求写入到 Memory Buffer，然后定时（默认是每隔 1 秒）写到 Filesystem Cache，这个从 Memory Buffer 到 Filesystem cache 的过程就叫 refresh
		- 在某些情况下，存在 Memory Buffer 和 Filesystem Cache 的数据可能会丢失，ES 是通过 translog 的机制来保证数据的可靠性。实现机制是接收到请求后，同时也会写入到 translog 中，当 Filesystem Cache 中的数据写入到磁盘中，才会清除掉，这个过程叫做 flush
		- 在 flush 过程中，内存中的缓冲将被清除，内容被写入一个新段，段的 fsync 将创建一个新的提交点，并将新内容刷新到磁盘；旧的 translog 将被删除并开始一个新的 translog。flush 触发的时机是定时触发（默认 30 分钟）或者 translog 变得太大（默认为 512M）时
- Why
- How
- How Good
- Refs
- See Also