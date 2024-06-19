- What
- Why
- How
	- 多态模式
		- 当集合中的所有文档都具有**相似但不相同**的结构时，我们将其称为多态模式；比如运动员的运动记录
		  ![](https://pdai.tech/images/db/mongo/mongo-y-doc-2.png){:height 252, :width 508}
		  ![](https://pdai.tech/images/db/mongo/mongo-y-doc-2.gif){:height 343, :width 508}
	- 属性模式
		- 为了优化搜索，可能需要许多索引以照顾到所有子集。创建这些索引可能会降低性能，为此我们可以采用属性模式
		- 例如，现在有一个关于电影的集合，其中所有的文档有类似的字段：标题、导演、制片人、演员、上映日期等等。如果要对上映日期这个字段进行搜索，这是面临的挑战是“哪个上映日期”？因为在不同的国家，电影的上映日期可能不同
		  ```json
		  {
		      title: "Star Wars",
		      director: "George Lucas",
		      ...
		      release_US: ISODate("1977-05-20T01:00:00+01:00"),
		      release_France: ISODate("1977-10-19T01:00:00+01:00"),
		      release_Italy: ISODate("1977-10-20T01:00:00+01:00"),
		      release_UK: ISODate("1977-12-27T01:00:00+01:00"),
		      ...
		  }
		  ```
		  使用属性模式，可以将此属性移至数组中，以减少对索引的需求。如下所示，这些信息被转换成一个包含键值对的数组
		  ```json
		  {
		      title: "Star Wars",
		      director: "George Lucas",
		      …
		      releases: [
		          {
		          location: "USA",
		          date: ISODate("1977-05-20T01:00:00+01:00")
		          },
		          {
		          location: "France",
		          date: ISODate("1977-10-19T01:00:00+01:00")
		          },
		          {
		          location: "Italy",
		          date: ISODate("1977-10-20T01:00:00+01:00")
		          },
		          {
		          location: "UK",
		          date: ISODate("1977-12-27T01:00:00+01:00")
		          },
		          … 
		      ],
		      … 
		  }
		  ```
	- 桶模式
		- 适用场景
			- 处理物联网（IOT）
			- 实时分析
			- 通用时间序列数据
		- 优点
			- 将数据放在一起，能更容易地将数据组织成特定的组
			- 提高发现历史趋势或预测未来的能力
			- 能对存储进行优化
		- 例子
			- 由于数据是在一段时间内持续流入（时间序列数据），我们可能倾向于将每个测量值存储在自己的文档中。然而，这种处理方式，非常偏向于关系型数据处理的方式
			- 例如，有一个传感器每分钟测量温度并将其保存到数据库中，数据流如下
			  ```json
			  {
			     sensor_id: 12345,
			     timestamp: ISODate("2019-01-31T10:00:00.000Z"),
			     temperature: 40
			  }
			  
			  {
			     sensor_id: 12345,
			     timestamp: ISODate("2019-01-31T10:01:00.000Z"),
			     temperature: 40
			  }
			  
			  {
			     sensor_id: 12345,
			     timestamp: ISODate("2019-01-31T10:02:00.000Z"),
			     temperature: 41
			  }
			  ```
			  利用桶模式，可以节省索引大小、简化潜在的查询、提高在文档中使用预聚合数据的能力。对上边的数据流应用桶模式，可以得到：
			  ```json
			  {
			      sensor_id: 12345,
			      start_date: ISODate("2019-01-31T10:00:00.000Z"),
			      end_date: ISODate("2019-01-31T10:59:59.000Z"),
			      measurements: [
			         {
			         timestamp: ISODate("2019-01-31T10:00:00.000Z"),
			         temperature: 40
			         },
			         {
			         timestamp: ISODate("2019-01-31T10:01:00.000Z"),
			         temperature: 40
			         },
			         … 
			         {
			         timestamp: ISODate("2019-01-31T10:42:00.000Z"),
			         temperature: 42
			         }
			      ],
			     transaction_count: 42,
			     sum_temperature: 2413
			  }
			  ```
- How Good
- Refs
- See Also