- Why
- What
	- **jps**（JavaVirtual Machine Process Status Tool）
		- 查看当前java进程
		- ```
		  jps –l # 输出输出完全的包名，应用主类名，jar的完全路径名
		  ```
	- **jstack**
		- 查看或导出 Java 应用程序中**线程堆栈信息**
		- ```
		  jstack 2815
		  jstack -m 2815 # java和native c/c++框架的所有栈信息
		  jstack -l 2815 # 额外的锁信息列表，查看是否死锁
		  ```
	- **jinfo**
		- 查看正在运行的 java 应用程序的扩展参数（包括Java System属性和JVM命令行参数）
		- 动态的修改正在运行的 JVM 一些参数
		- 当系统崩溃时，可以从core文件里面知道崩溃的Java应用程序的配置信息
		- ```
		  jinfo 2815 # 输出当前 jvm 进程的全部参数和系统属性
		  ```
	- **jmap**（多功能的命令）
		- 生成 java 程序的 dump 文件
		- 查看堆内对象示例的统计信息、查看 ClassLoader 的信息以及 finalizer 队列
		- ```
		  # 查看堆的情况
		  jmap -heap 2815
		  
		  # dump
		  jmap -dump:live,format=b,file=/tmp/heap2.bin 2815
		  ```
	- **jstat**
		- 参数众多，但是使用一个就够了
		- ```
		  jstat -gcutil 2815 1000 
		  ```
	-
- How
- How Good
- See Also
	-