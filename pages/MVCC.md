- What
	- 全称Multi-Version Concurrency Control，即多版本并发控制。MVCC是一种并发控制的方法，一般在数据库管理系统中，实现对数据库的并发访问，在编程语言中实现事务内存
- Why
- How
	- MySQL
	  id:: 6645cd5c-293a-4612-b1d7-d9ce2ed2b504
		- 在InnoDB引擎中，指在已提交读[[READ COMMITTED]]和可重复读[[REPEATABLE READ]]这两种隔离级别下的事务，对于SELECT操作会访问版本链中的记录的过程。
		  
		  这就使得别的事务可以修改这条记录，反正每次修改都会在版本链中记录。SELECT可以去版本链中拿记录，这就实现了读-写，写-读的并发执行，提升了系统的性能。
		- InnoDB引擎实现[[MVCC]]的基础点
			- **隐式字段**
			  logseq.order-list-type:: number
			  ![](https://pdai.tech/images/db/mysql/db-mysql-mvcc-1.png){:height 161, :width 688}
			  如上图，DB_ROW_ID是数据库默认为该行记录生成的唯一隐式主键；DB_TRX_ID是当前操作该记录的事务ID； 而DB_ROLL_PTR是一个回滚指针，用于配合undo日志，指向上一个旧版本；delete flag没有展示出来。
			- **undo log**
			  logseq.order-list-type:: number
			  ![](https://pdai.tech/images/db/mysql/db-mysql-mvcc-4.png)
			  从上面，我们就可以看出，不同事务或者相同事务的对同一记录的修改，会导致该记录的undo log成为一条记录版本线性表，既链表，undo log的链首就是最新的旧记录，链尾就是最早的旧记录
			- LATER **ReadView**
			  logseq.order-list-type:: number
			  已提交读和可重复读的区别就在于它们生成ReadView的策略不同。
			  
			  ReadView中主要就是有个列表来存储我们系统中**当前活跃着的读写事务**，也就是begin了还未提交的事务。通过这个列表来判断记录的某个版本是否对当前事务可见。假设当前列表里的事务id为[80,100]：
				- 如果你要访问的记录版本的事务id为50，比当前列表最小的id 80小，那说明这个事务在之前就提交了，所以对当前活动的事务来说是可访问的
				  logseq.order-list-type:: number
				- 如果你要访问的记录版本的事务id为90,发现此事务在列表id最大值和最小值之间，那就再判断一下是否在列表内，如果在那就说明此事务还未提交，所以版本不能被访问。如果不在那说明事务已经提交，所以版本可以被访问
				  logseq.order-list-type:: number
				- 如果你要访问的记录版本的事务id为110，那比事务列表最大id100都大，那说明这个版本是在ReadView生成之后才发生的，所以不能被访问
				  logseq.order-list-type:: number
			- 这些记录都是去undo log 链里面找的，先找最近记录，如果最近这一条记录事务id不符合条件，不可见的话，再去找上一个版本再比较当前事务的id和这个版本事务id看能不能访问，以此类推直到返回可见的版本或者结束。阿斯顿发
			- **举个例子** ，在已提交读隔离级别下：
			  
			  比如此时有一个事务id为100的事务，修改了name,使得的name等于小明2，但是事务还没提交。则此时的版本链是
			  
			  ![](https://pdai.tech/images/db/mysql/db-mysql-mvcc-11.jpeg)
			  
			  那此时另一个事务发起了select 语句要查询id为1的记录，那此时生成的ReadView 列表只有[100]。那就去版本链去找了，首先肯定找最近的一条，发现trx_id是100,也就是name为小明2的那条记录，发现在列表内，所以不能访问。
			- 这时候就通过指针继续找下一条，name为小明1的记录，发现trx_id是60，小于列表中的最小id,所以可以访问，直接访问结果为小明1。
			- 那这时候我们把事务id为100的事务提交了，并且新建了一个事务id为110也修改id为1的记录，并且不提交事务
			- ![](https://pdai.tech/images/db/mysql/db-mysql-mvcc-12.jpeg)
			- 这时候版本链就是
			- ![](https://pdai.tech/images/db/mysql/db-mysql-mvcc-13.jpeg)
			- 这时候之前那个select事务又执行了一次查询,要查询id为1的记录。
			  
			  **已提交读隔离级别下的事务在每次查询的开始都会生成一个独立的ReadView,而可重复读隔离级别则在第一次读的时候生成一个ReadView，之后的读都复用之前的ReadView**。
				- 如果你是已提交读隔离级别，这时候你会重新一个ReadView，那你的活动事务列表中的值就变了，变成了[110]。按照上的说法，你去版本链通过trx_id对比查找到合适的结果就是小明2。
				- 如果你是可重复读隔离级别，这时候你的ReadView还是第一次select时候生成的ReadView,也就是列表的值还是[100]。所以select的结果是小明1。所以第二次select结果和第一次一样，所以叫可重复读！
				  
				  这就是Mysql的MVCC,通过版本链，实现多版本，可并发读-写，写-读。通过ReadView生成策略的不同实现不同的隔离级别。
- How Good
- Refs
- See Also