- Why
- What
	- Java 9后续版本发布策略
		- 每3年发布一个TLS，长期维护版本。意味着Java 8 ，Java 11， Java 17 才可能被大规模使用
		- 每年发布两个正式版本，分别是3月份和9月份
	- Java 10
		- **并行全垃圾回收器 G1**
	- Java 11
		- **伸缩低延迟垃圾收集器** **ZGC** (Z Garbage Collector)
	- Java 14
		- Switch 表达式（正式版）
			- 之前：
				- ```
				  int dayOfWeek;
				  switch (day) {
				      case MONDAY:
				      case FRIDAY:
				      case SUNDAY:
				          dayOfWeek = 6;
				          break;
				      case TUESDAY:
				          dayOfWeek = 7;
				          break;
				      case THURSDAY:
				      case SATURDAY:
				          dayOfWeek = 8;
				          break;
				      case WEDNESDAY:
				          dayOfWeek = 9;
				          break;
				      default:
				          dayOfWeek = 0;
				          break;
				  }
				  ```
			- 之后：
				- ```
				  int dayOfWeek = switch (day) {
				      case MONDAY, FRIDAY, SUNDAY -> 6;
				      case TUESDAY                -> 7;
				      case THURSDAY, SATURDAY     -> 8;
				  case WEDNESDAY              -> 9;
				      default              -> 0;
				  
				  };
				  ```
		- **Records**
			- ```
			  public record Person(String name, int age) {
			      public static String address;
			  
			      public String getName() {
			          return name;
			      }
			  }
			  ```
			- 对上述代码进行编译，然后反编译之后可以看到如下结果
			- ```
			  public final class Person extends java.lang.Record {
			      private final java.lang.String name;
			      private final java.lang.String age;
			  
			      public Person(java.lang.String name, java.lang.String age) { /* compiled code */ }
			  
			      public java.lang.String getName() { /* compiled code */ }
			  
			      public java.lang.String toString() { /* compiled code */ }
			  
			      public final int hashCode() { /* compiled code */ }
			  
			      public final boolean equals(java.lang.Object o) { /* compiled code */ }
			  
			      public java.lang.String name() { /* compiled code */ }
			  
			      public java.lang.String age() { /* compiled code */ }
			  }
			  ```
- How
- How Good
- Refs
	- [Java全栈知识体系](https://pdai.tech/md/interview/x-interview.html#_6-2-java-9-%E7%89%B9%E6%80%A7)
- See Also