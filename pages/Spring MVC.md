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
	- TODO 工作原理
	  (中文叙述流程不清晰)
	  ![](https://pdai.tech/images/project/project-b-5.png)
		- 客户端请求到达 DispatcherServlet
		  logseq.order-list-type:: number
		  	•	当客户端（例如浏览器）发送一个请求时，这个请求首先到达 Spring MVC 的前端控制器 DispatcherServlet。这个控制器是整个 Spring MVC 流程的起点，负责接收所有的请求
		- DispatcherServlet 调用 HandlerMapping
		  logseq.order-list-type:: number
		  	•	DispatcherServlet 收到请求后，会根据请求的 URL 和其他信息调用 HandlerMapping。HandlerMapping 的作用是找到处理这个请求的适当的处理器（即控制器）
		- 找到相应的 Handler（Controller 控制器）
		  logseq.order-list-type:: number
		  	•	HandlerMapping 会根据请求的 URL 解析出对应的 Handler。在 Spring MVC 中，这个 Handler 通常是一个 Controller 控制器
		- HandlerAdapter 调用处理器
		  logseq.order-list-type:: number
		  id:: 66820050-7a68-4521-ab9e-70058fecdfd6
		  	•	找到相应的 Handler 后，DispatcherServlet 会通过 HandlerAdapter 来调用这个 Handler。HandlerAdapter 负责执行 Handler 中定义的业务逻辑
		- 处理器执行业务逻辑并返回 ModelAndView
		  logseq.order-list-type:: number
		  	•	Handler 执行完业务逻辑后，会返回一个 ModelAndView 对象。这个对象包含两个部分：Model（数据对象）和 View（视图名称）
		- ViewResolver 解析视图
		  logseq.order-list-type:: number
		  	•	DispatcherServlet 接收到 ModelAndView 后，会调用 ViewResolver 来解析 View 的逻辑名称，将其转化为实际的视图对象（通常是一个 JSP 页面或 Thymeleaf 模板）
		- 视图渲染：
		  logseq.order-list-type:: number
		  	•	解析出实际的视图后，DispatcherServlet 会将 Model 中的数据传递给视图对象进行渲染。视图对象将数据渲染成 HTML 格式
		- 返回视图给客户端
		  logseq.order-list-type:: number
		  	•	最后，渲染好的视图（HTML 页面）会作为响应返回给客户端（浏览器）。客户端收到响应后，将其展示给用户
- How Good
- Refs
- See Also