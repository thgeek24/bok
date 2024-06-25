- What
	- 6 种标识 #card
		- SYN
			- Synchronize
			- Initiate and establish a connection
		- ACK
			- Acknowledgment
			- Acknowledge the receipt of packets
		- PSH
			- Push
			- Push data to the receiving application immediately
		- FIN
			- Finish
			- Gracefully terminate a connection
		- RST
			- Reset
			- Abruptly terminate a connection
		- URG
			- Urgent
			- Indicate urgent data that should be processed quickly
	- [[三次握手]]
- Why
- How
	- [[SYN 洪泛攻击]]
	- [[TCP 四次挥手]]
	- 输入 URL 到页面加载过程
	  collapsed:: true
		- 地址栏输入 URL
		- [[DNS]]将域名解析到 IP
		- 请求和响应数据
			- 建立 TCP 连接（3 次握手）
			- 发送 HTTP 请求
			- 服务器处理请求
			- 返回 HTTP 响应结果
			- 关闭 HTTP 连接（4 次挥手）
		- 浏览器加载、解析和渲染
		  下图是在数据传输过程中的工作方式，在发送端是应用层-->链路层这个方向的封包过程，每经过一层都会增加该层的头部；而接收端则是从链路层-->应用层解包的过程，每经过一层就会去掉相应的首部
		  ![](https://pdai.tech/images/develop/network/dev-network-protocol-10.png){:height 404, :width 452}
	- 如何使用 netstat 查看服务及监听端口
	  collapsed:: true
		- ```sh
		  netstat -t/-u/-l/-r/-n  
		  # 显示网络相关信息
		  # -t:TCP;-u:UDP协议;-l:监听;-r:路由;-n:显示IP地址和端口号
		  ```
		- TODO 例如，查看本地监听的端口 
		  :LOGBOOK:
		  CLOCK: [2024-06-25 Tue 11:04:12]--[2024-06-25 Tue 11:04:13] =>  00:00:01
		  CLOCK: [2024-06-25 Tue 11:04:17]--[2024-06-25 Tue 11:04:18] =>  00:00:01
		  CLOCK: [2024-06-25 Tue 11:04:19]--[2024-06-25 Tue 11:04:20] =>  00:00:01
		  CLOCK: [2024-06-25 Tue 11:04:23]--[2024-06-25 Tue 11:04:24] =>  00:00:01
		  :END:
		  ```sh
		  [root@pdai-centos ~]# netstat -tlun
		  Active Internet connections (only servers)
		  Proto Recv-Q Send-Q Local Address           Foreign Address         State      
		  tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      
		  tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
		  tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN                          
		  udp        0      0 172.21.0.14:123         0.0.0.0:*                          
		  udp        0      0 127.0.0.1:123           0.0.0.0:*                          
		  udp6       0      0 fe80::5054:ff:fe2b::123 :::*                               
		  udp6       0      0 ::1:123                 :::* 
		  ```
	- [[TCPDump]]抓包
	- [[Wireshark]]抓包
- How Good
- Refs
- See Also