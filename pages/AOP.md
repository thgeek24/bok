- What
	- 定义
		- Aspect-Oriented Programming（面向切面编程）能够将那些与业务无关，却被多个业务模块共同调用的逻辑或责任（例如事务处理、日志管理，权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可扩展性和可维护性
	- 包含的概念
	  collapsed:: true
		- Joinpoint（连接点）
			- 具体的切面点点抽象概念，可以是在字段、方法上，Spring 中具体表现形式是 PointCut（切入点）
		- [[Advice]]（通知）
			- 在连接点进行的具体操作，如何进行增强处理的，分为前置、后置、异常、最终、环绕五种情况
		- 目标对象
			- 被 AOP 框架进行增强处理的对象，也被称为增强的对象
		- AOP 代理
			- AOP 框架创建的对象，简单的说，代理就是对目标对象的加强
			- Spring 中的 AOP 代理可以是[[JDK 动态代理]]，也可以是 [[CGLIB 代理]]
		- Weaving（织入）
			- 将增强处理添加到目标对象中，创建一个被增强的对象的过程
		- 总结
			- 在目标对象（target object）的某些方法（jointpoint）添加不同种类的操作（通知、增强处理），最后通过某些方法（weaving、织入操作）实现一个新的代理目标对象
	- 优点
		- 使用 AOP 之后，可以把一些通用功能抽象出来，在需要用到的地方直接使用，这样可以大大简化代码量
		- 也可以很方便地增加新功能，这样提高了系统的扩展性
		- 日志管理、事务管理和权限管理等场景都用到了 AOP
- Why
- How
	- AOP 的实现方式
		- 两大类
			- [[静态代理]]
			- [[动态代理]]
	- Spring AOP 的实现方式
		- 基于[[动态代理]]，如果要代理的对象实现了某个接口，那么 Spring AOP 就会使用 JDK 动态代理去创建代理对象
		- 而对于没有实现接口的对象，就无法使用[[JDK 动态代理]]，转而使用[[CGLib 动态代理]]生成一个被代理对象的子类来作为代理
		  ![](https://pdai.tech/images/spring/spring-interview-3.png){:height 230, :width 452}
		- 也可以使用[[AspectJ]](Spring AOP 中已经集成了 AspectJ）
	- 应用场景
		- 记录日志（调用方法后记录日志）
		- 监控性能（统计方法运行时间）
		- 权限控制（调用方法前校验是否有权限）
		- 事务管理（调用方法前开启事务，调用方法后提交关闭事务）
		- 缓存优化（第一次调用查询数据库，将查询结果放入内存对象；第二次调用，直接从内存返回对象，不需要查询数据库）
	- Spring AOP VS [[AspectJ]]
		- |特性|Spring AOP|AspectJ|
		  |增强方式|运行时增强|编译时增强|
		  |基础技术|代理（Proxying）|字节码操作（Bytecode Manipulatioin）|
		  |集成情况|Spring AOP 集成了 AspectJ|独立存在，Java 生态系统中最完整的 AOP 框架|
		  |功能强大程度|相对简单|功能更强大|
		  |性能差异（切面较少时）|差异不大|差异不大|
		  |性能差异（切面较多时）|性能较慢|性能较快|
		- 详细解释 #card
			- 增强方式
				- Spring AOP：在运行时通过代理对象实现方法增强
				- AspectJ：在编译时通过字节码操作实现方法增强
			- 基础技术
				- Spring AOP：基于代理机制，使用 JDK 动态代理或 CGLIB 代理
				- AspectJ：通过直接操作字节码来实现增强，提供更细粒度的控制
			- 集成情况
				- Spring AOP：与 Spring 框架无缝集成，可以结合使用 AspectJ 的部分功能
				- AspectJ：作为独立的 AOP 框架，提供了 Java 生态系统中最完整的 AOP 解决方案
			- 功能强大程度
				- Spring AOP：提供的功能相对简单，适合大多数常见的 AOP 场景
				- AspectJ：功能更加强大，适用于更复杂的 AOP 需求
			- 性能差异
				- 当切面较少时，两者性能差异不大
				- 当切面较多时，AspectJ 的性能优于 Spring AOP，因为 AspectJ 的字节码操作在执行时更加高效
- How Good
- Refs
- See Also