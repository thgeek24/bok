- What
	- 定义
		- MVC
			- 一种设计模式
		- Spring MVC
			- 一款很优秀的 MVC 框架
	- 优点
		- 便于进行更简洁的 Web 层的开发
		- 天生与 Spring 框架集成
	- 对后端项目的分层
		- Service 层（服务层）
		- Dao 层（数据层）
		- Entity 层（实体层）
		- Controller 层（控制层，返回数据给前台页面）
	- 简单原理图
		- ![](https://pdai.tech/images/spring/spring-interview-6.png)
- Why
- How
	- 工作原理
	  ![](https://pdai.tech/images/project/project-b-5.png)
		- 客户端（浏览器）发送请求，直接请求到 DispatcherServlet
		  logseq.order-list-type:: number
		- DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler
		  logseq.order-list-type:: number
		- 解析到对应的 Handler（也就是我们平常说的Controller控制器）
		  logseq.order-list-type:: number
		- HandlerAdapter 会根据 Handler 来调用真正的处理器来处理请求和执行相对应的业务逻辑
		  logseq.order-list-type:: number
		- 处理器处理完业务后，会返回一个 ModelAndView 对象，Model 是返回的数据对象，View 是逻辑上的 View
		  logseq.order-list-type:: number
		- ViewResolver 会根据逻辑 View 去查找实际的 View
		  logseq.order-list-type:: number
		- DispatcherServlet 把返回的 Model 传给View（视图渲染）
		  logseq.order-list-type:: number
		- 把 View 返回给请求者（浏览器）
		  logseq.order-list-type:: number
- How Good
- Refs
- See Also