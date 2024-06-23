- What
- Why
	- 使用 Lombok 时，只要添加了相应的注解，就无需再为此写任何代码。自动生成的代码到底是如何产生的呢？
	- 核心之处就是对于注解的解析上，JDK 5 引入了注解的同时，也提供了两种解析方式
- How
	- 运行时解析
		- 运行时能够解析的注解，必须将 @Retention 设置为 RUNTIME，即 `@Retention(RetentionPolicy.RUNTIME`，这样就可以通过反射拿到该注解
		- TODO java.lang.reflect 反射包中提供了一个接口 AnnotatedElement，该接口定义了获取注解信息的几个方法，Class、Constructor、Field、Method、Package 等都实现了该接口
	- 编译时解析
		- Annotation Processing Tool
		  logseq.order-list-type:: number
			- apt 自 JDK 5 产生，JDK 7 已标记为过期，JDK 8 中已彻底删除
			- 自 JDK 6 开始，可以使用 Pluggable Annotation Processing API 来替换
			- 被替换原因
				- api 都在 com.sun.mirror非标准包下
				- 没有继承到 javac 中，需要额外运行
		- [[Pluggable Annotation Processing API]]
		  logseq.order-list-type:: number
			- 解决了 apt 上述的两个问题
			- javac 在执行的时候会调用实现了该 API 的程序，这样我们就可以对编译器做一些增强，这是 javac 执行的过程如下
			  ![](https://pdai.tech/images/develop/package/dev-package-lombok-2.png)
	- Lombok 与[[JSR 269 API]]
		- 在使用 javac 的过程中，它产生作用的具体流程如下
			- javac 对源代码进行分析，生成了一颗抽象语法树（AST）
			- 运行过程中调用实现了“JSR 269 API”的 Lombok 程序
			- 此时 Lombok 就对第一步骤得到的 AST 进行处理，找到 @Data 注解所在类对应的语法树（AST），然后修改该语法树（AST），增加 getter 和 setter 方法定义的相应树节点
			- javac 使用修改后的抽象语法树（AST）生成字节码文件，即给 class 增加新的节点（代码块）
			  ![](https://pdai.tech/images/develop/package/dev-package-lombok-3.png){:height 763, :width 353}
			  从上面 Lombok 执行的流程图中可以看出：
			- 在 javac 解析成AST 抽象语法树之后，Lombok 根据自己编写的注解处理器，动态地修改 AST，增加新的节点（即 Lombok 自定义注解所需要生成的代码），最终通过分析生成JVM可执行的字节码Class文件
			- 使用Annotation Processing自定义注解是在编译阶段进行修改，而JDK的反射技术是在运行时动态修改，两者相比，反射虽然更加灵活一些但是带来的性能损耗更加大
- How Good
- Refs
- See Also