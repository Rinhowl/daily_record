# Spring Boot集成Mybatis框架

了解到现在实习的公司用的Spring Boot和Mybatis这两套技术，恰好最近都还没有分配到任务，所以就先带薪学习一下吧。

下面先快速实现一个Spring Boot集成MyBatis的demo，然后再深入学习Mybatis的特性，虽然之前学过，但是已经忘了！是全忘了的那种，因为之前也是拿官方文档进行学习的，又没有事件，所以很容易遗忘。这一次同样是拿官方文档来学习，然后希望可以在接下来的项目中进行实战，巩固下来。

附上官方文档的连接：http://www.mybatis.org/mybatis-3/zh/index.html  

#### Hello World

先不管这个技术的细节如何、API如何，想要学习一门技术，首先要看看他能实现什么样的效果对吧，所以，先来快速实现一个集成的demo，了解其集成的work flow。

**1. 添加依赖**
```xml
    <dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.2</version>
		</dependency>

    <dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
```

**2. 配置数据库连接**  
在application.properties文件中配置连接数据库的配置：
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/test?serverTimezone=UTC  
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

**3. 编写PO**
```java
package top.zhonglinhao.entity;

public class User {
	private int id;
	private String name;
	private int age;

	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
}
```

**4. 编写Mapper接口**
```java
package top.zhonglinhao.mapper;

import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;

import top.zhonglinhao.entity.User;

@Mapper
public interface UserMapper {

	@Select("select * from user where name = #{name}")
	User findByName(@Param("name") String name);

	@Insert("insert into user(name,age) values(#{name}, #{age})")
	int addUser(@Param("name") String name, @Param("age") int age);

}

```

**5. 编写controller进行测试**
```java
package top.zhonglinhao.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import top.zhonglinhao.entity.User;
import top.zhonglinhao.mapper.UserMapper;

@RestController
public class TestController {

	@Autowired
	private UserMapper userMapper;

	@GetMapping("/hello")
	public String hello() {
		return "hello world!";
	}

  @GetMapping("/add/{name}/{age}")
	public String add(@PathVariable("name") String name, @PathVariable("age") int age) {
		User user = new User();
		user.setName(name);
		user.setAge(age);
		return "添加了" + userMapper.addUser(user.getName(), user.getAge()) + "条数据";
	}

	@GetMapping("/find/{name}")
	public User add(@PathVariable("name") String name) {
		return userMapper.findByName(name);
	}
}
```

**6. 接口测试**  
1) `http://localhost:8080/add/lisi/13`:添加了1条数据  
2）`http://localhost:8080/find/lisi`:{"id":2,"name":"lisi","age":13}
