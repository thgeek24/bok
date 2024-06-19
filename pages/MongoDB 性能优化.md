- What
- Why
- How
	- 慢查询
		- 为了定位查询，需要查看当前[[mongo profile]]的级别
		  ```json
		  db.getProfilingLevel()
		  ```
		  显示为 0，表示默认下是没有记录的
		- 设置 profile 级别
		  ```json
		  db.setProfilingLevel(1, 1000)
		  ```
		  (记录慢查询模式，所有超过 1000ms 的查询语句都会被记录下来)
- How Good
- Refs
- See Also