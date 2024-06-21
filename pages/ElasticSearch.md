- What
	- 编写语言
		- [[Java]]
	- 索引和搜索
		- 内部使用[[Lucene]]
	- 目的
		- 使全文检索变得简单
	- 方式
		- 隐藏[[Lucene]]的复杂性，取而代之的是提供一套简单一致的 RESTful API
	- 定义
		- 不仅仅是[[Lucene]]，也不仅仅是只一个全文搜索引擎
			- 一个分布式的实时文档存储，每个字段都可以被索引与搜索
			- 一个分布式实时分析搜索引擎
			- 能胜任上百个服务节点的扩展，并支持 PB 级别的结构化或者非结构化数据
	- 数据库 VS ElasticSearch
		- |RDBMS|Elasticsearch|
		  |数据库（database）|索引（index）|
		  |表（table）|类型（type）（6.0.0 废弃）|
		  |行（row）|文档（document）|
		  |列（column）|字段（field）|
		  |表结构（schema）|映射（mapping）|
		  |索引|反向索引|
		  |SQL|查询 DSL|
		  |SELECT * FROM TABLE|GET http://...|
		  |UPDATE table SET|PUT http://...|
		  |DELETE|DELETE http://...|
- Why
- How
	- [[ELK]]技术栈的常见应用场景
		- 日志系统
		  ![](https://pdai.tech/images/db/es/es-introduce-2-5.png){:height 190, :width 422}
		- 增加数据源，和使用MQ
		  ![](https://pdai.tech/images/db/es/es-introduce-2-6.png){:height 229, :width 452}
		- Metric收集和APM性能监控
		  ![](https://pdai.tech/images/db/es/es-introduce-2-7.png)
	- [[ES 索引]]
	- [[ES 查询和聚合]]
	- [[ES 的结构和底层实现]]
	- [[ES 内部如何读取文件的]]
	- [[ES 内部索引文档]]
	- [[ES 底层数据持久化]]
	-
- How Good
- Refs
- See Also
-