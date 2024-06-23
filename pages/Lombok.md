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
			- 迫使同项目组全体成员使用，如果组内有一个人使用了 lombok，那么其他人都必须安装 IDE 插件
			- 如果我们需要升级到某个新版本的 JDK 的时候，若其中的特性在Lombok中不支持的话就会受到影响
		- Lombok 破坏了[[封装]]性
			- 如下，定义一个购物车类：
			  ```java
			  @Data
			  public class ShoppingCart { 
			      //商品数目
			      private int itemsCount; 
			  
			      //总价格
			      private double totalPrice; 
			  
			      //商品明细
			      private List items = new ArrayList<>();
			  }
			  
			  //例子来源于《极客时间-设计模式之美》
			  ```
				- 我们知道，购物车中商品数目、商品明细以及总价格三者之前其实是有关联关系的，如果需要修改的话是要一起修改的
				- 但是，我们使用了Lombok的`@Data`注解，对于itemsCount 和 totalPrice这两个属性。虽然我们将它们定义成 `private` 类型，但是提供了 `public` 的 `getter`、`setter` 方法
				- 外部可以通过 `setter` 方法随意地修改这两个属性的值。我们可以随意调用 `setter` 方法，来重新设置 itemsCount、totalPrice 属性的值，这也会导致其跟 items 属性的值不一致
				- 所以，暴露不应该暴露的 setter 方法，明显违反了面向对象的封装特性
				- 好的做法应该是不提供`getter/setter`，而是只提供一个 public 的 addItem 方法，同时去修改 itemsCount、totalPrice 以及 items 三个属性。（所以不能一股脑使用 @Data 注解）
				- 此外，[[Java 14]]提供 record 语法糖，来解决类似问题
				  ```java
				  public record Range(int min, int max) {}
				  ```
- How
- How Good
- Refs
- See Also
-