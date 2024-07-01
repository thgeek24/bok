- What
	- 定义
	  collapsed:: true
		- Spring Framework，一种轻量级框架，旨在提高开发人员的开发效率以及系统的可维护性
		- 是多个模块的集合：
			- Core Container（核心容器）
			- 数据访问/集成
			- Web
			- [[AOP]]（面向切面编程）
			- 工具
			- 消息和测试模块
		- 部分模块的具体说明
			- Core Container 中的 Core 组件是 Spring 所有组件的核心
			- Beans 组件和 Context 组件是实现[[IOC]]和[[DI]]的基础
			- [[AOP]]组件用来实现面向切面编程
		- 六个特征
			- 核心技术
				- [[依赖注入]]（DI），[[AOP]]，事件（Events），资源，[[i18n]]，验证，数据绑定，类型转换，SpEL
			- 测试
				- 模拟对象，TestContext 框架，Spring MVC 测试，WebTestClient
			- 数据访问
				- 事务，DAO 支持，JDBC，ORM，编组 XML
			- Web 支持
				- Spring MVC 和 Spring WebFlux Web 框架
			- 集成
				- 远程处理，JMS，JCA，JMX，电子邮件，任务，调度，缓存
			- 语言
				- Kotlin，Groovy，动态语言
	- 重要的 Spring 模块
	  collapsed:: true
		- 下图对应的是 Spring 4.X 的版本，目前最新的 5.X 版本中 Web 模块的 Portlet 组件已经被废弃掉，同时增加了用于异步响应式处理的 WebFlux 组件
		  ![](https://pdai.tech/images/spring/spring-interview-1.png)
			- Spring Core
				- 基础，可以说 Spring 其他所有的功能都依赖于该类库
				- 主要提供[[IOC]]和 DI 功能
			- Spring Aspects
				- 为 AspectJ 的集成提供支持
			- Spring AOP
				- 提供面向切面的编程实现
			- Spring JDBC
				- Java 数据库连接
			- Spring JMS
				- Java 消息服务
			- Spring ORM
				- 用于支持 Hibernate 等 ORM 工具
			- Spring Web
				- 为创建 Web 应用程序提供支持
			- Spring Test
				- 提供了对 JUnit 和 TestNG 测试的支持
- Why
- How
	- [[Spring Bean]]
	- [[Spring MVC]]
	- [[Spring 框架用到的设计模式]]
	- @Component 和 @Bean 的区别
	  collapsed:: true
		- 区别
			- 作用对象不同
				- @Component 注解作用于*类*，而 @Bean 注解作用于*方法*
			- 注册方式不同
				- @Component 注解通过类路径扫描自动检测并装配到 Spring 容器中，而 @Bean 注解通过标注在方法上来定义 Bean，并由该方法返回实例进行装配
			- 自定义性和应用场景不同
				- @Bean 注解比 @Component 注解具有更强的自定义性，并且在某些情况下只能通过 @Bean 注解来注册 Bean，比如装配第三方库的类
		- 示例
			- @Bean
			  ```java
			  @Configuration
			  public class AppConfig {
			      @Bean
			      public TransferService transferService() {
			          return new TransferServiceImpl();
			      }
			  }
			  ```
			- XML
			  ```xml
			  # 上面的 java 代码相当于下面的 XML 配置
			  <beans>
			      <bean id="transferService" class="com.yanggb.TransferServiceImpl"/>
			  </beans>
			  ```
			- 只能通过 @Bean 而不能通过 @Component 实现的示例
			  ```java
			  @Bean
			  public OneService getService(status) {
			      case (status)  {
			          when 1:
			                  return new serviceImpl1();
			          when 2:
			                  return new serviceImpl2();
			          when 3:
			                  return new serviceImpl3();
			      }
			  }
			  ```
	- [[Spring 事务]]
	- [[Bean Factory]] 和 [[ApplicationContext]]的区别
		- ApplicationContext 提供了一种解析文本消息的方法，一种加载文件资源（如图像）的通用方法，它们可以将事件发不到注册为侦听器的 bean
		- 可以在应用程序上下文中以声明方式处理容器中的容器或容器上的操作，这些操作必须以编程方式与 Bean Factory 一起处理
		- ApplicationContext 实现 MessageSource，一个用于获取本地化消息的接口，实际的实现是可插入的
- How Good
- Refs
- See Also