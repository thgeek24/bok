- What
	- 定义
	  collapsed:: true
		- 是 Spring 开源组织下的子项目，是 Spring 组件一站式解决方法
		- 主要降低了使用 Spring 的难度，简化了繁重的配置，提供了各种启动器，使得开发者能快速上手
	- 具体优点
	  collapsed:: true
		- 简化 Spring 应用的初始搭建以及开发过程，使用特定的方式来进行配置
		- 能够创建独立运行的 Spring 应用程序，并通过 main 方法启动
		- 嵌入的 tomcat，无需将应用程序打包为 WAR 文件进行部署
		- 简化 maven 配置
		- 自动配置 Spring 应用，并通过相应的功能启动器（starter）实现自动化配置
		- 简化 Spring 应用开发，约定大于配置，去繁化简
- Why
	- 使用原因
	  collapsed:: true
		- 独立运行
			- Spring Boot 内嵌了各种 servlet 容器，比如 Tomcat、Jetty 等，不需要打成 WAR 包部署到容器中，而只需要达成一个可执行的 jar 包就能独立运行，所有依赖包都在一个 jar 包内
		- 简化配置
			- spring-boot-starter-web 启动器自动依赖其他组件，减少了 maven 配置
		- 自动配置
			- Spring Boot 能根据当前类路径下的类、jar 包来自动配置 bean，比如添加一个 spring-boot-starter-web 启动器就能拥有 web 的功能，无需其他配置
		- 无代码生成和 XML 配置
			- Spring Boot 配置过程中无代码生成，也无需 XML 配置文件就能完成所有配置工作，这一切都是借助于条件注解完成的，这也是 Spring 4.x 的核心功能之一
		- 应用监控
			- Spring Boot 提供一系列端点可以监控服务和应用，做[[健康检测]]
- How
	- [[Spring]]、[[Spring MVC]]和 Spring Boot 的区别
	  collapsed:: true
		- Spring
			- 最重要的特征是[[依赖注入]]
			- 所有的 Spring Modules 不是[[依赖注入]]就是[[控制反转]]
			- 当恰当使用 DI 或 IOC 时，可以开发松耦合应用
		- Spring MVC
			- 提供了一种分离式的方法来开发 Web 应用；通过运用像[[DispatcherServlet]]，[[ModelAndView]]和[[ViewResolver]]等一些简单的概念，开发 Web 应用将会变得非常简单
		- Spring Boot
			- Spring 和 Spring MVC 的问题在于需要配置大量的参数
			- Spring Boot 通过一个自动配置和启动的项来解决这个问题
	- 核心注解
	  collapsed:: true
		- @SpringBootApplication
		  写在启动类上面的注解，也是 Spring Boot 的核心注解，包含以下注解
			- @SpringBootConfiguration
				- 组合了@Configuration注解，实现配置文件的功能
			- @EnableAutoConfiguration
				- 开启自动配置的功能
				- 也可以关闭某个自动配置的选项，如关闭数据源自动配置的功能：
					- @SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
			- @ComponentScan
				- Spring 组件扫描
	- [[Spring Boot Starter]]
	- [[spring-boot-starter-parent]]
	- [[spring-boot-maven-plugin]]
	- Spring Boot 打成的 jar 包和普通 jar 包的区别
	  collapsed:: true
		- 区别
			- 两者结构不同
				- Spring Boot 的 jar 包
					- 解压后，在`\BOOT-INF\classes`目录下才是编写的代码
				- 普通 jar 包
					- 解压后，直接就是包名，包里是编写的代码
			- 前者无法被其他项目依赖，即使依赖了已无法使用其中的类
				- 在`\BOOT-INF\classes`目录下的代码无法被直接引用
		- Spring Boot 打成的 jar 包
			- 是可执行 jar，可以直接通过`java -jar xxx.jar`命令来运行
			- 如果非要被引用，可以在 pom.xml 文件中增加配置，将 Spring Boot 项目打包成两个 jar，一个可执行，一个可引用
	- 异常处理
		- ControllerAdvice
			- 实现一个 ControllerAdvice 类，来处理控制类抛出的所有异常
	- [[热部署]]
	  collapsed:: true
		- 实现方式
			- Spring Loaded
			- spring-boot-devtools
	- 监视器
		- [[Spring Boot Actuator]]
- How Good
- Refs
- See Also
-