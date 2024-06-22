- What
	- 示例
	  collapsed:: true
		- `@Getter/@Setter`示例
		  ```java
		  @Setter(AccessLevel.PUBLIC)
		  @Getter(AccessLevel.PROTECTED)
		  private int id;
		  private String shap;
		  ```
		- `@ToString`示例
		  ```java
		  @ToString(exclude = "id", callSuper = true, includeFieldNames = true)
		  public class LombokDemo {
		      private int id;
		      private String name;
		      private int age;
		      public static void main(String[] args) {
		          //输出LombokDemo(super=LombokDemo@48524010, name=null, age=0)
		          System.out.println(new LombokDemo());
		      }
		  }
		  ```
		- `@EqualsAndHashCode`示例
		  ```java
		  @EqualsAndHashCode(exclude = {"id", "shape"}, callSuper = false)
		  public class LombokDemo {
		      private int id;
		      private String shap;
		  }
		  ```
- Why
	- 为什么使用
		- 通常需要编写大量代码才能使类变得有用。如以下内容：
			- `toString()`
			- `hashCode()`和`equals()`
			- `Getter`和`Setter`
			- 构造函数
			  对于这种简单的类，这些方法通常是无聊的、重复的，而且是可以很容易地机械地生成的那种东西（IDE 通常提供这种功能）
	- 为什么有的公司禁止使用
	  需要理解部分注解的底层，以避免潜在问题
		- @Data
			- 如果只使用了`@Data`，而不使用`@EqualsAndHashCode(callSuper=true)`的话，会默认是`@EqualsAndHashCode(callSuper=false)`,这时候生成的`equals()`方法只会比较子类的属性，不会考虑从父类继承的属性，无论父类属性访问权限是否开放
		- 可读性、可调试性低
			- 在代码中使用了 Lombok，确实可以帮忙减少很多代码，因为 Lombok 会帮忙自动生成很多代码。但是**这些代码是要在编译阶段才会生成的**，所以在开发的过程中，其实很多代码其实是缺失的
		- 有很强的侵入性
			- 迫使同项目组成员都得使用，如果组内有一个人使用了 lombok，那么其他人都必须安装 IDE 插件
			-
- How
- How Good
- Refs
- See Also
-