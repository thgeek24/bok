- What
	- 方式
		- DSL
			- 基于文本
				- match
				- query string
			- 基于词项
				- term
			- 复合查询
				- 5 种
		- EQL（Elastic Query Language）
			- bucket
			- metric
			- pipeline
		- SQL
	- 复合查询
	  collapsed:: true
		- 定义
			- 多种条件组合的查询
		- 方式
			- bool query（布尔查询）
				- 通过布尔逻辑将较小的查询组合成较大的查询
			- boosting query（提高查询）
				- 不同于 bool 查询，bool 查询中只要一个子查询条件不匹配，那么搜索的数据就不会出现；而 boosting query 则是降低显示的权重/优先级（即 score）
			- constant_score（固定分数查询）
				- 查询某个条件时，固定的返回指定的 score；显然当不需要计算 score 时，只需要 filter 条件即可，因为 filter context 忽略 score
			- dis_max（最佳匹配查询）
				- 分离最大化查询（Disjunction Max Query）指的是：将任何与任一查询匹配的文档作为结果返回，但只将最佳匹配的评分作为查询的评分结果返回
			- function_score（函数查询）
				- 简而言之，就是自定义 function 来计算 _score
	- Bucket 聚合
	  collapsed:: true
		- TODO (原文不完整) 在设计上大概分为三类（当然有些是第二和第三类的融合）
		  ![](https://pdai.tech/images/db/es/es-agg-bucket-1.png)
	- Metric 聚合
		- 从分类看
			- 单值分析
			- 多值分析
		- 从功能看
			- 根据具体的应用场景设计了一些分析 API，比如地理位置，百分数等等
		- 单值分析：只输出一个分析结果
			- 标准 stat 型
				- `avg`平均值
				- `max`最大值
				- `min`最小值
				- `sum`和
				- `value_count`数量
			- 其他类型
				- `cardinality`基数（distinct 去重）
				- `weighted_avg`带权重的 avg
				- `median_absolute_deviation`中位值
		- 多值分析：单值之外的
			- stats 型
				- `stats`包含 avg，max，min，sum 和 count
				- `matrix_stats`针对矩阵模型
				- `extended_stats`
				- `string_stats`针对字符串
			- 百分数型
				- `percentiles`百分数范围
				- `percentile_ranks`百分数排行
			- 地理位置型
				- `geo_bounds`百分数范围
				- `geo_centroid`百分数排行
				- `geo_line`
			- Top 型
				- `top_hits`分桶后的 top hits
				- `top_metrics`
	- Pipeline 聚合
		- 含义
			- 让上一步的聚合结果成为下一个聚合的输入
		- 从不同维度理解
			- 第一个维度
				- 管道聚合有很多不同**类型**，每种类型都与其他聚合计算不同的信息，但是可以将这些类型分为两类
					- **父级** 父级聚合的输出提供了一组管道聚合，它可以计算新的存储桶或新的聚合以添加到现有存储桶中
					- **兄弟** 同级聚合的输出提供的管道聚合，并且能够计算与该同级聚合处于同一级别的新聚合
			- 第二个维度
				- 根据**功能设计**的意图
					- 比如前置聚合可能是 Bucket 聚合，后置可能是 Metric 聚合，那么它就可以成为一类管道
					  进而引出了：`xxx bucket`
					- Bucket 聚合 -> Metric 聚合：Bucket 聚合的结果，成为下一步 Metric 聚合的输入
						- Average bucket
						- Min bucket
						- Max bucket
						- Sum bucket
						- Stats bucket
						- Extended stats bucket
						- ...
- Why
- How
	- query 和 filter 的区别
	  collapsed:: true
		- query 是查询 + score
		- filter 仅包含查询
		  如下所示，复合查询中的 constant_score 查询无需计算 score，所以对应查询是 filter 而不是 query
		  ```sh
		  GET /test-dsl-constant/_search
		  {
		    "query": {
		      "constant_score": {
		        "filter": {
		          "term": { "content": "apple" }
		        },
		        "boost": 1.2
		      }
		    }
		  }
		  ```
	- match 和 term 的区别
	  collapsed:: true
		- match 基于文本
		- term 基于索引的词项
		  如下三中查询等同
		  ```sh
		  GET /test-dsl-match/_search
		  {
		      "query": {
		          "match": {
		              "title": "BROWN DOG"
		          }
		      }
		  }
		  
		  GET /test-dsl-match/_search
		  {
		    "query": {
		      "match": {
		        "title": {
		          "query": "BROWN DOG",
		          "operator": "or"
		        }
		      }
		    }
		  }
		  
		  GET /test-dsl-match/_search
		  {
		    "query": {
		      "bool": {
		        "should": [
		          {
		            "term": {
		              "title": "brown"
		            }
		          },
		          {
		            "term": {
		              "title": "dog"
		            }
		          }
		        ]
		      }
		    }
		  }
		  ```
	- should 和 must 的区别
	  collapsed:: true
		- should 是任意匹配
		  ```sh
		  GET /test-dsl-match/_search
		  {
		    "query": {
		      "bool": {
		        "should": [
		          {
		            "term": {
		              "title": "brown"
		            }
		          },
		          {
		            "term": {
		              "title": "dog"
		            }
		          }
		        ]
		      }
		    }
		  }
		  ```
		- must 是同时匹配
		  ```sh
		  GET /test-dsl-match/_search
		  {
		    "query": {
		      "match": {
		        "title": {
		          "query": "BROWN DOG",
		          "operator": "and"
		        }
		      }
		    }
		  }
		  ```
		  等同于
		  ```sh
		  GET /test-dsl-match/_search
		  {
		    "query": {
		      "bool": {
		        "must": [
		          {
		            "term": {
		              "title": "brown"
		            }
		          },
		          {
		            "term": {
		              "title": "dog"
		            }
		          }
		        ]
		      }
		    }
		  }
		  ```
	- match，match_phrase 和 match_phrase_prefix 有什么区别
	  collapsed:: true
		- match 本质是对 term 组合
		- match_phrase 本质是连续的 term 的查询（and 关系）
		- match_phrase_prefix 在 match_phrase 基础上提供了一种可以查最后一个词项是前缀的方法
		  例如，某个字段内容是“quick brown fox”，如果我们需要查询包含“quick brown f”，就需要使用 match_phrase_prefix，因为 f 不是完整的 term 分词，不能用 match_phrase
- How Good
- Refs
- See Also