- What
	- 定义
		- 第三方提供的工具，基于[[ASM]]实现，性能比较高
	- 特点
		- 无需通过接口来实现，它是针对类实现代理，主要是对指定的类生成一个子类，通过实现子类的方式来完成调用的
	- 原理
		- 对指定目标类生成一个字类，并覆盖其中方法实现增强
		- 但因为采用的是继承，所以不能对 final 修饰的类进行代理
	- 调用流程
		- 通过调用拦截器的 intercept 方法来实现对被代理类的调用
		- 拦截逻辑可以写在 intercept 方法的 invokeSuper(o, objects) 的前后实现拦截
	- 示例
		- ```java
		  public class CGLibDemo {
		    // 需要动态代理的实际对象
		    static class Sister  {
		        public void sing() {
		            System.out.println("I am Jinsha, a little sister.");
		        }
		  }
		    static class CGLibProxy implements MethodInterceptor {
		    private Object target;
		    public Object getInstance(Object target){
		            this.target = target;
		            Enhancer enhancer = new Enhancer();
		            // 设置父类为实例类
		            enhancer.setSuperclass(this.target.getClass());
		            // 回调方法
		            enhancer.setCallback(this);
		            // 创建代理对象
		            return enhancer.create();
		        }
		  
		    @Override
		        public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
		            System.out.println("introduce yourself...");
		            Object result = methodProxy.invokeSuper(o,objects);
		            System.out.println("score...");
		            return result;
		        }
		    }
		   
		    public static void main(String[] args) {
		        CGLibProxy cgLibProxy = new CGLibProxy();
		        //获取动态代理类实例
		        Sister proxySister = (Sister) cgLibProxy.getInstance(new Sister());
		        System.out.println("CGLib Dynamic object name: " + proxySister.getClass().getName());
		        proxySister.sing();
		    }
		  }
		  ```
- Why
	- [[JDK 动态代理]]只能代理实现接口的类，一般没有实现接口的类不能进行代理
	- 使用 CGLib 实现动态代理，完全不受代理类必须实现接口的限制
- How
- How Good
- Refs
- See Also