- Why
- What
- How
	- 被它注解的接口只能有一个抽象方法，有两种意外
		- 接口允许有实现的方法，这种实现的方法是用default关键字来标记的(java反射中java.lang.reflect.Method\#isDefault()方法用来判断是否是default方法)
		  logseq.order-list-type:: number
		- ```
		  java public interface Comparator<T> { 
		  	int compare(T o1, T o2); 
		      boolean equals(Object obj); 
		  }
		  ```
- How Good
- Refs
- See Also