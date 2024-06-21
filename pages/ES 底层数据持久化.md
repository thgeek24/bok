- What
	- 步骤
		- write
		- refresh
		- flush
		- merge
	- 步骤详情
		- write
		  ![](https://pdai.tech/images/db/es/es-th-2-6.png)
			- 一个新文档过来，会存储在 in-memory buffer 内存缓存区中，顺便会记录 Translog（Elasticsearch 增加了一个 translog（事务日志），在每一次对 Elasticsearch 进行操作时均进行了日志记录）
			- 这时候数据还没到 segment，是搜不到这个新文档的。数据只有被 refresh 后，才可以被搜索到
		- refresh
		  ![](https://pdai.tech/images/db/es/es-th-2-7.png){:height 306, :width 452}
		  refresh 默认 1 秒钟执行以此上图流程（通过 index.refresh_interval 设置 refresh 间隔时间）
			- in-memory buffer 中的文档写入新的 segment 中，但 segment 是存储在文件系统的缓存中。此时文档可以被搜索到
			- 最后清空 in-memory buffer。注意：Translog 没有被清空，为了将 segment 数据写到磁盘
			- 文档经过 refresh 后，segment 暂时写到文件系统缓存，这样避免了性能 IO 操作，又可以使文档搜索到。refresh 默认 1 秒执行一次，性能损耗太大。一般建议稍微延长这个 refresh 时间间隔，比如 5s。因此，ES 其实就是[[准实时]]，达不到真正的实时
		- flush
			- 每隔一段时间（例如 translog 变得越来越大）索引被刷新（flush）；一个新的 translog 被创建，并且一个全量提交被执行
			  ![](https://pdai.tech/images/db/es/es-th-2-9.png){:height 287, :width 422}
			  上个过程中（refresh），segment 在文件系统缓存中，会有意外故障文档丢失。那么，为了保证文档不会丢失，需要将文档写入磁盘；文档从文件缓存写入磁盘的过程就是 flush。写入磁盘后，清空 translog。具体过程如下：
				- 所有在内存缓冲区的文档都被写入一个新的段
				- 缓存区被清空
				- 一个 commit point 被写入硬盘
				- 文件系统缓存通过 fsync 被刷新（flush）
				- 老的 translog 被删除
		- merge
			- 由于自动刷新流程每秒都会创建一个新的段，这样会导致短时间内的段数量暴增。段数目太多会带来较大的麻烦，每一个段都会消耗文件句柄、带宽和 CPU 运行周期；更重要的是，每个搜索请求都必须轮流检查每个段；所以段越多，搜索也就越慢
			- Elasticsearch 通用后台进行 Merge Segment 来解决这个问题。小的段被合并到大的段，然后这些段被合并到更大的段
			- 当索引的时候，刷新（refresh）操作会创建新的段并将段打开以供搜索使用。合并进程选择一小部分大小相似的段，并在后台将它们合并到更大的段中。这并不会中断索引和搜索
			  ![](https://pdai.tech/images/db/es/es-th-2-10.png)
			- 一旦合并结束，老的段被删除
				- 新的段被刷新（flush）到了磁盘。写入一个包含新段且排除旧的和较小的段的新提交点
				- 新的段被打开用来搜索
				- 老的段被删除
			- ![](https://pdai.tech/images/db/es/es-th-2-11.png)
			- 合并大的段需要消耗大量的 I/O 和 CPU 资源，如果任其发展会影响搜索性能。Elasticsearch 在默认情况下会对合并流程进行资源限制，所以搜索仍然有足够的资源很好的执行
- Why
- How
- How Good
- Refs
- See Also