- What
	- 四种隔离级别
		- READ UNCOMMITTED
		  collapsed:: true
			- 定义
				- 读未提交
			- 存在的问题
				- 脏读
				- 不可重复读
				- 幻读
		- READ COMMITTED
		  collapsed:: true
			- 定义
				- 读提交
			- 新解决的问题
				- 脏读
			- 存在的问题
				- 不可重复读
				- 幻读
		- REPEATABLE READ
		  collapsed:: true
			- 定义
				- 可重复读
			- 新解决的问题
				- 不可重复读
			- 存在的问题
				- 幻读
		- SERIALIZABLE
		  collapsed:: true
			- 定义
				- 串行
			- 存在的问题
				- 不支持并发，太影响性能
- Why
- How
- How Good
- Refs
- See Also