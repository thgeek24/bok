- What
	- 定义
		- pub/sub，一个消息通信模式：发布者 (pub) 发布消息，订阅者 (sub) 接收消息
	- 命令
		- SUBSCRIBE 和 PUBLISH
			- SUBSCRIBE 可以让客户端订阅任意数量的频道， 每当有新信息发送到某个频道时， 信息就会被发送给所有订阅该频道的客户端
			- 如下图，展示了频道channel1，以及订阅该频道的三个客户端（client2，client5 和 client1）之间的关系
			  ![](https://pdai.tech/images/db/redis/db-redis-sub-1.svg)
			- 当有新消息通过 PUBLISH 命令发送给频道 channel1 时，这个消息就会被发送到订阅它的三个客户端
			  ![](https://pdai.tech/images/db/redis/db-redis-sub-2.svg)
- Why
	- 发布/订阅的两种方式
		- 基于频道（channel）的发布/订阅
			- “发布/订阅”模式包含两种角色，分别是发布者和订阅者；发布者可以向指定的频道(channel)发送消息; 订阅者可以订阅一个或者多个频道(channel)，所有订阅此频道的订阅者都会收到此消息
			  ![](https://pdai.tech/images/db/redis/db-redis-sub-8.png)
		- 基于模式（pattern）的发布/订阅
			- 下图展示了一个带有频道和模式的例子，其中`tweet.shop.*`模式匹配了`tweet.shop.ipad`和`tweet.shop.kindle`频道，并且有不同的客户端分别订阅了它们三个
			  ![](https://pdai.tech/images/db/redis/db-redis-sub-5.svg)
				- 当有消息发送到 tweet.shop.kindle 频道时，信息除了发送给 clientX 和 clientY 之外，还会发送给订阅 tweet.shop.* 模式的 client123 和 client26
				  ![](https://pdai.tech/images/db/redis/db-redis-sub-6.svg)
				- 类似的，如果接收到时信息的是频道 tweet.shop.ipad，那么 client123 和 client256 同样会收到信息
				  ![](https://pdai.tech/images/db/redis/db-redis-sub-7.svg)
- How
- How Good
- Refs
- See Also