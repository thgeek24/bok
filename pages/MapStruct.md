- What
	- 定义
		- 一款非常实用的 Java 工具，主要用于解决对象之间的拷贝问题，比如 PO/DTO/VO/QueryParam 之间的转换问题。区别于 BeanUtils 通过反射，它通过编译器编译生成常规方法，将可以很大程度上提升效率
	- 举例
		- ```java
		  @Mapper
		  public interface UserConverter {
		      UserConverter INSTANCE = Mappers.getMapper(UserConverter.class);
		  
		      @Mapping(target = "gender", source = "sex")
		      @Mapping(target = "createTime", dateFormat = "yyyy-MM-dd HH:mm:ss")
		      UserVo do2vo(User var1);
		  
		      @Mapping(target = "sex", source = "gender")
		      @Mapping(target = "password", ignore = true)
		      @Mapping(target = "createTime", dateFormat = "yyyy-MM-dd HH:mm:ss")
		      User vo2Do(UserVo var1);
		  
		      List<UserVo> do2voList(List<User> userList);
		  
		      default List<UserVo.UserConfig> strConfigToListUserConfig(String config) {
		          return JSON.parseArray(config, UserVo.UserConfig.class);
		      }
		  
		      default String listUserConfigToStrConfig(List<UserVo.UserConfig> list) {
		          return JSON.toJSONString(list);
		      }
		  }
		  ```
- Why
- How
- How Good
- Refs
- See Also