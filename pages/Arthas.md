- Why
	- 需要在线调试或诊断运行中的Java程序
- What
	- A diagnostic tool for Java applications
		- Often used to diagnose and troubleshoot running Java applications
		- Allow to debug production issues without the need for additional code changes or restarting the application
- How
	- **查看最繁忙的线程，以及是否有阻塞情况发生**?
	  collapsed:: true
		- ```
		  thread -n 3 # 查看最繁忙的三个线程栈信息
		  thread  # 以直观的方式展现所有的线程情况
		  thread -b #找出当前阻塞其他线程的线程
		  ```
	- **确认某个类是否已被系统加载**?
	  collapsed:: true
		- ```
		  # 即可以找到需要的类全路径，如果存在的话
		  sc *MyServlet
		  
		  # 查看这个某个类所有的方法
		  sm pdai.tech.servlet.TestMyServlet *
		  
		  # 查看某个方法的信息，如果存在的话
		  sm pdai.tech.servlet.TestMyServlet testMethod
		  ```
	- **如何查看一个class类的源码信息**?
		- ```
		  # 直接反编译出java 源代码，包含一此额外信息的
		  jad pdai.tech.servlet.TestMyServlet
		  ```
	- **如何跟踪某个方法的返回值、入参**?
		- ```
		  # 同时监控入参，返回值，及异常
		  watch pdai.tech.servlet.TestMyServlet testMethod "{params, returnObj, throwExp}" -e -x 2
		  ```
	- **如何看方法调用栈的信息**?
		- ```
		  stack pdai.tech.servlet.TestMyServlet testMethod
		  ```
	- **找到最耗时的方法调用**?
	  collapsed:: true
		- ```
		  # 执行的时候每个子调用的运行时长，可以找到最耗时的子调用。
		  stack pdai.tech.servlet.TestMyServlet testMethod
		  ```
	- **如何临时更改代码运行**?
	  collapsed:: true
		- ```
		  # 先反编译出class源码
		  jad --source-only com.example.demo.arthas.user.UserController > /tmp/UserController.java  
		  
		  # 然后使用外部工具编辑内容
		  mc /tmp/UserController.java -d /tmp  # 再编译成class
		  
		  # 最后，重新载入定义的类，就可以实时验证你的猜测了
		  redefine /tmp/com/example/demo/arthas/user/UserController.class
		  ```
	- **我如何测试某个方法的性能问题**?
		- ```
		  monitor -c 5 demo.MathGame primeFactors
		  ```
- How Good
- Refs
	- [Java全栈知识点问题汇总：问题排查](https://pdai.tech/md/interview/x-interview.html#_5-4-%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5)
- See Also