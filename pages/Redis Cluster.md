- What
	- Redis-cluster 是一种服务器[[Sharding 技术]]，Redis 3.0 以后版本正式提供支持
	- [[Redis 哈希槽]]
- Why
- How
	- 写操作丢失
		- Redis 不能保证数据的强一致性，意味着在实际中集群在特定条件下可能会丢失写操作
- How Good
- Refs
- See Also