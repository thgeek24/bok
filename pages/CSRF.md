- What
	- 定义
		- Cross-Site Request Forgery，攻击这盗用了用户的身份，以用户的名义发送恶意请求
	- 示意图
		- ![](https://pdai.tech/images/security/dev-security-csrf-x1.jpeg)
- Why
- How
	- 黑客能拿到 Cookie 吗？
	  collapsed:: true
		- CSRF 攻击中，黑客借助受害者的 cookie 骗取服务器的信任，但是黑客并不能拿到 cookie，也看不到 cookie 的内容
		- 对于服务器返回的结果，由于[[浏览器同源策略]]的限制，黑客也无法进行解析；因此，黑客无法从返回的结果中得到任何东西，他所能做的就是给服务器发送请求，以执行请求中所描述的命令，在服务器端直接改变数据的值，而非窃取服务器中的数据
	- 什么样的请求需要防护 CSRF
		- 有的框架（比如 Spring Security），防护 CSRF 的 filter 限定的 Method 是 POST/PUT/DELETE 等，而没有限定 GET Method
		- 这是因为需要保护的对象，是那些可以直接产生数据改变的服务，而对于读取数据的服务，则不需要进行 CSRF 的保护；通常 GET请求只查询数据，并不对服务器数据进行修改，所以这些框架没有拦截 GET 请求
		- 例如，银行系统中转账的请求会直接改变账户的金额，会遭到 CSRF 攻击，需要保护；而查询余额是对金额的读取操作，不会改变数据，且 CSRF 攻击无法解析服务器返回的结果，无需保护
	- 为什么对请求做了 CSRF 拦截，但还是会报 CSRF
	  collapsed:: true
		- 在前端已经采用 POST+CSRF token 请求，后端也对 POST 请求做了 CSRF Filter，但是渗透测试中还有 CSRF 漏洞？
		- ```java
		  // 这里没有限制POST Method，导致用户可以不通过POST请求提交数据
		  // 这一点容易被忽视
		  @RequestMapping("/url")
		  public ReponseData saveSomething(XXParam param){
		      // 数据保存操作...
		  }
		  ```
	- CSRF 防御常规思路
	  collapsed:: true
		- 验证[[HTTP]]Referer 字段
			- 根据 HTTP 协议，在 HTTP 头中有一个字段叫 Referer，它记录了该 HTTP 请求的来源地址，只需要验证 Referer
		- 在请求地址中添加 token 并验证
			- 在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token
			- 如果请求中没有 token 或者 token 不正确，则认为是 CSRF 攻击而拒绝该请求
			- 这种方法比检查 Referer 更安全一些，token 可以在用户登陆后产生并放于 session 之中，然后在每次请求时把 token 从 session 中拿出，与请求中的 token 进行比对；但这种方法的难点在于如何把 token 以参数的形式加入请求
		- 在 HTTP 头中自定义属性并验证
	- 开发中如何防御 CSRF
		- 可以自定义 xxxCsrfFilter 去拦截实现，可参考Spring Security-`org.springframework.security.web.csrf.CsrfFilter.java`
- How Good
- Refs
- See Also