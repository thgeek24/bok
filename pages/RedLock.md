- What
	- TODO 定义
		- 用于实现 Redis 分布式锁
- Why
- How
	- 搞多个 Redis master 部署，以保证它们不会同时宕掉
	- 这些 master 节点是完全相互独立的，它们之间不存在数据同步
	- 需要确保在这多个 master 实例上，是与在 Redis 单实例，使用相同方法来获取和释放锁
- How Good
- Refs
- See Also