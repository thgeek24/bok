- What
- Why
	- 简化配置（和[[自动配置]]的目的一样）
	- 解决依赖管理配置较为复杂的问题
		- 避免了遍历所有的依赖包（一个一个地添加到项目的依赖管理中）
- How
	- 使用场景
		- 需要构建一个 Web 应用程序时
	- 使用方法
	  collapsed:: true
		- 配置一个 spring-boot-starter-web
		  ```xml
		  <dependency>
		      <groupId>org.springframework.boot</groupId>
		      <artifactId>spring-boot-starter-web</artifactId>
		  </dependency>
		  ```
	- 自定义 Spring Boot Starter
		- 实现功能
		- 添加 Properties
		  ```java
		  @Data
		  @ConfigurationProperties(prefix = "com.pdai")
		  public class DemoProperties {
		      private String version;
		      private String name;
		  }
		  ```
		- 添加 AutoConfiguration
		  ```java
		  @Configuration
		  @EnableConfigurationProperties(DemoProperties.class)
		  public class DemoAutoConfiguration {
		  
		      @Bean
		      public com.pdai.demo.module.DemoModule demoModule(DemoProperties properties){
		          com.pdai.demo.module.DemoModule demoModule = new com.pdai.demo.module.DemoModule();
		          demoModule.setName(properties.getName());
		          demoModule.setVersion(properties.getVersion());
		          return demoModule;
		  
		      }
		  }
		  ```
		- 添加 spring.factory
		  在 META-INF 下创建 spring.factory 文件
		  ```sh
		  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
		  com.pdai.demospringbootstarter.DemoAutoConfiguration
		  ```
		- install
		  ![](https://pdai.tech/images/spring/springboot-starter-demo-2.png)
- How Good
	- spring-boot-starter-data-jpa
	  collapsed:: true
		- 引入持久化功能
	- 其他启动器项目，包括用于开发特定类型应用程序的典型依赖项
	  collapsed:: true
		- spring-boot-starter-web-services
			- SOAP Web Services
		- spring-boot-starter-web
			- Web 和 RESTful 应用程序
		- spring-boot-starter-test
			- 单元测试和集成测试
		- spring-boot-starter-jdbc
			- 传统的 JDBC
		- spring-boot-starter-hateoas
			- 为服务添加 HATEOAS 功能
		- spring-boot-starter-security
			- 使用 SpringSecurity 进行身份验证和授权
		- spring-boot-starter-data-jpa
			- 带有 Hibernate 的 Spring Data JPA
		- spring-boot-starter-data-rest
			- 使用 Spring Data REST 公布简单的 REST 服务
- Refs
- See Also