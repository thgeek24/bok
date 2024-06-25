- What
	- 定义
		- 一段强大的网络[[抓包]]工具，它使用[[libcap]]库来抓取网络数据包，这个库在几乎在所有的 Linux/Unix 中都有
	- 常用参数
		- ```sh
		  tcpdump -i eth0 -nn -s0 -v port 80
		  ```
			- -i：选择要捕获的接口，通常是[[以太网卡]]或[[无线网卡]]，也可以是 vlan 或其他特殊接口。如果该系统上只有一个网络接口，则无需指定
			- -nn：单个 n 表示不解析域名，直接显示 IP；两个 n 标识不解析
- Why
- How
- How Good
- Refs
- See Also
-