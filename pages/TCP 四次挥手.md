- What
	- 定义 #card
		- ![](https://pdai.tech/images/develop/network/dev-network-protocol-x2.png)
		  （下面的*主动断开方*与*被动断开方*，为客户端与服务端，或者服务端与客户端）
		- 主动断开方
		  logseq.order-list-type:: number
			- 发送一个 FIN，用来关闭*主动断开方*到*被动断开方*的数据传送
		- 被动断开方
		  logseq.order-list-type:: number
			- 收到这个 FIN，发回一个 ACK，确认序号为收到的 seq+1
			- 和 SYN 一样，一个 FIN 将占用一个序号
			  id:: 6679289b-5527-4b79-bff5-323ce8e47d8e
		- 被动断开方
		  logseq.order-list-type:: number
			- 关闭与*主动断开方*的连接，发送一个 FIN 给*主动断开方*
		- 主动断开方
		  logseq.order-list-type:: number
			- 发回 ACK 报文确认，并将确认序号设置为收到的 seq+1
- Why
	- 为什么连接的时候是三次握手，关闭的时候却是四次握手
		- 建立连接时，服务器在 LISTEN 状态下，接收到客户端的 SYN 消息后，会回复 SYN+ACK 消息
		- 关闭连接时，服务器收到客户端的 FIN 消息（FIN 消息表示客户端不再发送数据，但还能接收消息）既可以选择立即关闭，也可以继续发一些消息后再关闭，因此服务器的 ACK 和 FIN 消息分成了两次发，因此多了一次挥手
- How
- How Good
- Refs
- See Also