- What
	- 定义
		- Inversion Of Control（控制反转）是一种设置思想，将原本在程序中手动创建对象的控制权，交给 IOC 容器来管理，并由 IOC 容器完成对象的注入
	- 优点
		- 简化应用的开发，把应用从复杂的依赖关系中解放出来
		- IOC 容器就像一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的
	- 实现原理
		- 工厂模式 + 反射机制
	- 示例
	  ```java
	  interface Fruit {
	       public abstract void eat();
	  }
	  
	  class Apple implements Fruit {
	      public void eat(){
	          System.out.println("Apple");
	      }
	  }
	  
	  class Orange implements Fruit {
	      public void eat(){
	          System.out.println("Orange");
	      }
	  }
	  
	  class Factory {
	      public static Fruit getInstance(String ClassName) {
	          Fruit f=null;
	          try {
	              f=(Fruit)Class.forName(ClassName).newInstance();
	          } catch (Exception e) {
	              e.printStackTrace();
	          }
	          return f;
	      }
	  }
	  
	  class Client {
	      public static void main(String[] a) {
	          Fruit f=Factory.getInstance("io.github.dunwu.spring.Apple");
	          if(f!=null){
	              f.eat();
	          }
	      }
	  }
	  ```
- Why
- How
- How Good
- Refs
- See Also