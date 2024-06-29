- What
	- 生命周期
		- 经历的方法调用分类
		  collapsed:: true
			- Bean 自身的方法
				- bean 本身调用的方法
				- 配置文件中`<bean>`的 init-method 和 destroy-method 指定的方法
			- Bean 级生命周期接口方法
				- BeanNameAware
				- BeanFactoryAware
				- ApplicationContextAware
				- InitializingBean 和 DisposableBean 这些接口的方法（可以被 @PostConstruct 和 @PreDestroy 注解替代）
			- 容器级生命周期接口方法
			  包括如下两个接口；它们的实现类被称为“后处理器”
				- InstantiationAwareBeanPostProcessor
				- BeanPostProcessor
			- 工厂后处理器接口方法
			  包括如下方法；工厂后处理器也是容器级的；在应用上下文装配配置文件之后立即调用
				- AspectJWeavingEnabler
				- ConfigurationClassPostProcessor
				- CustomAutowireConfigurer
		- 具体流程
		  ![](https://pdai.tech/images/spring/springframework/spring-framework-ioc-source-102.png){:height 272, :width 452}
			- 如果 BeanFactoryBeanProcessor 和 Bean 关联，则调用postProcessBeanFactory 方法（即首先尝试从 Bean 工厂中获取 Bean）
			  ---
			- 如果 InstantiationAwareBeanPostProcessor 和 Bean 关联，则调用 postProcessBeforeInstantiation 方法
			- 根据配置情况调用 Bean 构造方法**实例化 Bean**
			- 利用依赖注入完成 Bean 中所有**属性值的配置注入**
			- 如果 InstantiationAwareBeanPostProcessor 和 Bean 关联，则调用 postProcessAfterInstantiation 方法和 postProcessProperties
			- 调用 xxxAware 接口（上图只是给了几个例子）
				- 第一类 Aware 接口
					- 如果 Bean 实现了 BeanNameAware 接口，则 Spring 调用 Bean 的 setBeanName() 方法传入当前 Bean 的 id 值
					- 如果 Bean 实现了 BeanClassLoaderAware 接口，则 Spring 调用 setBeanClassLoader() 方法传入 classLoader 的引用
					- 如果 Bean 实现了 BeanFactoryAware 接口，则 Spring 调用 setBeanFactory() 方法传入当前工厂实例的引用
				- 第二类 Aware 接口
					- 如果 Bean 实现了 EnvironmentAware 接口，则 Spring 调用 setEnvironment() 方法传入当前 Environment 实例的引用
					- 如果 Bean 实现了 EmbeddedValueResolverAware 接口，则 Spring 调用 setEmbeddedValueResolver() 方法引入当前 StringValueResolver 实例的引用
					- 如果 Bean 实现了ApplicationContextAware 接口，则 Spring 调用 setApplicationContext() 方法引入当前 ApplicationContext 实例的引用
					- ...
			- 如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用该接口的预初始化方法 postProcessBeforeInitialzation() 对 Bean 进行加工操作，此处非常重要，Spring 的 AOP 就是利用它实现的
			- 如果 Bean 实现了 InitializingBean 接口，则 Spring 将调用 afterPropertiesSet() 方法（或者有执行 @PostConstruct 注解的方法）
			- 如果在配置文件中通过`init-method`属性指定了初始化方法，则调用该初始化方法
			- 如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用 afterPropertiesSet()
			- 如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用该接口的初始化方法 postProcessAfterInitialization()。此时，Bean 已经可以被应用系统使用了。
			- 如果在 `<bean>` 中指定了该 Bean 的作用范围为 scope="singleton"，则将该 Bean 放入 Spring IoC 的缓存池中，将触发 Spring 对该 Bean 的生命周期管理；如果在 `<bean>` 中指定了该 Bean 的作用范围为 scope="prototype"，则将该 Bean 交给调用者，调用者管理该 Bean 的生命周期，Spring 不再管理该 Bean
			- 如果 Bean 实现了 DisposableBean 接口，则 Spring 会调用 destroy() 方法将 Spring 中的 Bean 销毁；(或者有执行@PreDestroy注解的方法)
			- 如果在配置文件中通过 **destory-method** 属性指定了 Bean 的销毁方法，则 Spring 将调用该方法对 Bean 进行销毁
- Why
- How
	- 单例 bean 的线程安全
	  collapsed:: true
		- What
		- Why
			- TODO (静态成员变量的写操作就不会存在问题？)当多个线程操作同一个对象的时候，对这个对象的非静态成员变量的写操作会存在线程安全问题
		- How
			- 常见的两种解决方法
				- 在 bean 对象中尽量避免定义可变的成员变量（不太现实）
				- 在类中定义一个[[ThreadLocal]]成员变量，将需要的可变成员变量保存在[[ThreadLocal]]中（推荐的一种方式）
		- How Good
		- Refs
		- See Also
- How Good
- Refs
- See Also