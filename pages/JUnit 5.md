- What
	- 由单个不同子项目中的几个不同模块组成的
		- [[JUnit Platform]]
		- [[JUnit Jupiter]]
		- [[JUnit Vintage]]
	- 架构图
		- ![](https://pdai.tech/images/develop/ut/dev-ut-1.png)
	- JUnit 5 VS JUnit 4
		- |JUnit 4|JUnit 5|注释|
		  |@Test|@Test|表示该方法是一个测试方法|
		  |@BeforeClass|@BeforeAll|表示使用了该注解的方法应该在当前类中所有测试方法**之前**执行（只执行一次），并且它必须是 static 方法（除非 @TestInstance 指定生命周期为 Lifecycle.PER_CLASS）|
		  |@AfterClass|@AfterAll|表示使用了该注解的方法应该在当前类中所有测试方法**之后**执行（只执行一次），并且它必须是 static 方法（除非 @TestInstance 指定生命周期为 Lifecycle.PER_CLASS）|
		  |@Before|@BeforeEach|表示使用了该注解的方法应该在当前类中每一个测试方法**之前**执行|
		  |@After|@AfterEach|表示使用了该注解的方法应该在当前类中每一个测试方法**之后**执行|
		  |@Ignore|@Disabled|用于禁用（或者说忽略）一个测试类或测试方法|
- Why
- How
	- 在开发中可用来做单元测试的框架
		- JUnit 4/5
		- [[Mockito]]，mock 测试
		- [[Powermock]]，静态 util 的测试
- How Good
- Refs
- See Also