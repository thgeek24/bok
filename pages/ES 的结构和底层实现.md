- What
	- 整体结构
		- 一个 ES index 在集群模式下，有多个 Node（节点）组成。每个节点就是 ES 的 instance（实例）
		- 每个节点上会有多个 shard（分片），P1、P2 是主分片，R1、R2 是副本分片
		- 每个分片上对应着就是一个 Lucene Index（底层索引文件）
		- Lucene Index 是一个统称
			- 由多个 Segment（段文件，就是倒排索引）组成。每个段文件存储着就是 Doc 文档
			- commit point 记录了所有 segments 的信息
			  ![](https://pdai.tech/images/db/es/es-th-2-3.png)
	- 底层和数据文件
		- 倒排索引（词典+倒排表）
		- doc values - 列式存储
		- 正向文件 - 行式存储
		  ![](https://pdai.tech/images/db/es/es-th-2-2.png)
		- 文件的关系如下
		  ![](https://pdai.tech/images/db/es/es-th-3-2.jpeg)
- Why
- How
- How Good
- Refs
- See Also