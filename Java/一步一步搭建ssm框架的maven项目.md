# 一步一步搭建ssm框架的Maven项目
  
**第一步：创建一个Maven项目**  

+ 勾选 `Create a simple project(skip archetype selection)`;  
+ 填写工程信息`Group Id(工程组id)`、`Artifact id(工程名)`，版本号为`0.0.1-SNAPSHOT`，打包方式选择为`war`。

**第二步：解决新建maven项目报错的问题**

+ 创建好Maven项目的时候，pom.xml出现小红叉。   
 【解决方法】在WEB-INF中加入web.xml即可。具体做法：右击项目中的Deployment Descriptor,点击Generate Deployment Descriptor Stub

+ 创建好Maven项目的时候，java Source出现小红叉。  
【解决方法】在problem中找到错误是Project configuration is not up-to-date with pom.xml。具体做法：右击该错误，然后点击quick fix。

**第三步：在pom.xml中进行初步设置**

+ 设置编码格式以及jar文件版本号  

```xml
	<properties>
		<!-- 文件拷贝时的编码 -->
		<project.bulid.sourceEncoding>UTF-8</project.bulid.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>

		<!-- 配置版本号 -->
		<spring.version>4.2.5.RELEASE</spring.version>
		<mybatis.version>3.2.8</mybatis.version>
		<mysql-driver.version>5.1.29</mysql-driver.version>
		<slf4j.version>1.7.18</slf4j.version>
		<log4j.version>1.2.17</log4j.version>
	</properties>
```
+ 设置Maven的编译级别为1.8，配置文件路径(如果不设置发布到服务器启动后会找不到配置文件)  

```xml
	<build>
		<plugins>
			<plugin>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
				</configuration>
			</plugin>
		</plugins>
		<!-- 资源路径-->
		<resources>  
            <resource>  
                <directory>src/main/java</directory>  
                <includes>  
                    <include>**/*.properties</include>  
                    <include>**/*.xml</include>  
                </includes>  
                <filtering>false</filtering>  
            </resource>  
            <resource>  
                <directory>src/main/resources</directory>  
                <includes>  
                    <include>**/*.properties</include>  
                    <include>**/*.xml</include>  
                </includes>  
                <filtering>false</filtering>  
            </resource>  
        </resources> 
        
	</build>
```  


**第四步：准备项目结构以及必要的配置文件**

+ 基本的项目结构：  

>top.zhonglinhao.entity  
>top.zhonglinhao.mapper  
>top.zhonglinhao.service  
>top.zhonglinhao.controller

+ 必要的配置文件：bean.xml、springmvc.xml、db.properties、SqlMapConfig.xml  


**第五步：引入JavaWeb开发的依赖包**

```xml
		<dependency>
			<groupId>javax</groupId>
			<artifactId>javaee-api</artifactId>
			<version>7.0</version>
			<scope>provided</scope>
		</dependency>
```


**第六步：引入Spring和Springmvc依赖jar**  

```xml
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context-support</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-expression</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-beans</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>commons-logging</groupId>
			<artifactId>commons-logging</artifactId>
			<version>1.1.1</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-oxm</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjweaver</artifactId>
			<version>1.8.10</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.11</version>
			<scope>test</scope>
		</dependency>
```

**第七步：web.xml配置**

+ post乱码配置

```xml
	<!-- post乱码过滤器 -->
	<filter>
		<filter-name>CharacterEncodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>utf-8</param-value>
		</init-param>
	</filter>

	<filter-mapping>
		<filter-name>CharacterEncodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```
+ Spring配置，加载bean.xml，由容器创建ioc容器

```xml
	<!-- spring配置 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:bean.xml</param-value><!-- 特别注意这个路径是发布到服务器的文件路径 -->
	</context-param>

	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
```
+ SpringMVC配置，配置前端核心处理器，捕获并处理请求

```xml
	<servlet>
		<servlet-name>springmvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:springmvc.xml</param-value>
		</init-param>
	</servlet>
	<servlet-mapping>
		<servlet-name>springmvc</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
```



**第八步：配置springmvc.xml**

```xml
        <!-- 1,开启注解驱动 -->
        <!-- 
        开启注解驱动之后，就不需要再xml配置文件中配置控制器和处理器映射器了。
        		1)置控制器是为了将控制器注入到ioc容器中
        		2)配置处理器映射器是将请求的uri映射到Controller方法中
        		3)另外，如果开启了注解驱动之后，也无需再配置处理器适配器了。
        		所谓的处理器适配器，就是指定以何种方式实现并执行Handler。
         -->
        <mvc:annotation-driven></mvc:annotation-driven>
        
        <!-- 2，配置包扫描，扫描Controller包，配置的相当于是控制器 -->
        <context:component-scan base-package="top.zhonglinhao.controller"></context:component-scan>
        
        <!-- 3，配置视图解析器，常用的视图解析器为InternalResourceViewResolver -->
        <bean id="jspViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        		<property name="prefix" value="/WEB-INF/test/"></property>
        		<property name="suffix" value=".jsp"></property>
        </bean>
```


**第九步：搭建Controller测试进行页面**  

```java
package top.zhonglinhao.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class TestController {
	
	@RequestMapping("/hello")
	public String hello() {
		System.out.println("请求到达Controller！");
		return "hello";
	}
}
```  
**页面跳转成功！**  

----
**第十步： 引入MyBatis依赖及MySql依赖的jar文件**  

```xml
		<!-- mybatis -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>${mybatis.version}</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.2.2</version>
		</dependency>
		<!-- 使用c3p0连接池 -->
		<dependency>
			<groupId>com.mchange</groupId>
			<artifactId>c3p0</artifactId>
			<version>0.9.5.2</version>
		</dependency>

		<!-- Mysql 驱动依赖 -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>${mysql-driver.version}</version>
		</dependency>
``` 

**第十一步：MyBatis全局配置SQLMapConfig.xml**    

```xml
	<!-- setting -->
	 <settings> 
		<!-- 日志 -->
		<!-- <setting name="logImpl" value="LOG4J"/>
		延迟加载 -->
		 <setting name="lazyLoadingEnabled" value="true"/> 
		<!-- 消极加载（按需加载） -->
		<!-- <setting name="aggressiveLazyLoading" value="true"/> -->
		<!-- 二级缓存 -->
		<!-- <setting name="cacheEnabled" value="true"/> -->
	 </settings> 
	
	<!-- 开启别名 -->
	<!-- 开启别名之后就不需要在mapper.xml中写类的全路径了 -->
	<typeAliases>
		<package name="top.zhonglinhao.entity"/>
	</typeAliases>
```

**第十二步：数据库配置、事务配置等，交由Spring的ioc容器管理（bean.xml）**  

```xml
	<!-- 1,包扫描 -->
	<context:component-scan
		base-package="top.zhonglinhao.mapper, top.zhonglinhao.service"></context:component-scan>

	<!-- 2,加载数据库连接配置文件：解决数据库配置硬编码的问题 -->
	<context:property-placeholder location="classpath:db.properties"/>
	
	<!-- 3,数据源配置，使用c3p0连接池 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${driver}"></property>
		<property name="jdbcUrl" value="${url}"></property>
		<property name="user" value="${username}"></property>
		<property name="password" value="${password}"></property>

		<!-- 连接池中保留的最大连接数，默认为15 -->
		<property name="maxPoolSize" value="${c3p0.pool.maxPoolSize}"></property>
		<!-- 连接池中默认的最小连接数，默认为15 -->
		<property name="minPoolSize" value="${c3p0.pool.minPoolSize}"></property>
		<!-- 初始化时创建的连接数 -->
		<property name="initialPoolSize" value="${c3p0.pool.initialPoolSize}"></property>
		<!-- 定义从数据库获取新连接失败后重复尝试获取的次数，默认为30 -->
		<property name="acquireIncrement" value="${c3p0.pool.acquireIncrement}"></property>
	</bean>
	
	<!-- 4，配置sqlSessionFactory -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
		<!-- 整合MyBatis，包扫描Mapper文件 -->
		<property name="configLocation" value="classpath:SqlMapConfig.xml"></property>
		<property name="mapperLocations" value="classpath:top/zhonglinhao/mapper/*.xml"></property>
	</bean>
	
	<!-- 5,Mapper动态代理扫描 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="top.zhongloinhao.mapper"></property>
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
	</bean>
	
	<!-- 6,事务管理 -->
	<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<!-- 7,事务增强 -->
	<tx:advice id="txAdvice" transaction-manager="txManager">
		<tx:attributes>
			<tx:method name="insert*" propagation="REQUIRED" />
			<tx:method name="update*" propagation="REQUIRED" />
			<tx:method name="delete*" propagation="REQUIRED" />
			<tx:method name="save*" propagation="REQUIRED" />
			<tx:method name="find*" read-only="false" />
			<tx:method name="get*" read-only="false" />
			<tx:method name="view*" read-only="false" />
		</tx:attributes>
	</tx:advice>
	
	<!-- 8,AOP配置，拦截方法，应用事务增强 -->
	<aop:config>
		<!-- 定义切入点——拦截哪些方法 -->
		<aop:pointcut expression="execution(* top.zhonglinhao.service.*.*(..))"
			id="txPointcut" />
		<!-- 应用事务增强 -->
		<aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut" />
	</aop:config>
```
**第十三步：编写Entity、Mapper和Mapper.xml调用进行测试**

+ Admin.java

```java

public class Admin implements Serializable {
	
	private int id;
	private String name;
	private int age;
	
	//getter & setter
}
```
+ AdminMapper.java  

```java
package top.zhonglinhao.mapper;

import top.zhonglinhao.entity.Admin;

public interface AdminMapper {
	
	public Admin findAll();

}
```
+ AdminMapper.xml  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="top.zhonglinhao.mapper.AdminMapper">

	<select id="findAll" resultType="top.zhonglinhao.entity.Admin">
		SELECT * FROM tb_admin where id=20;
	</select>
	
</mapper>
```
+ IAdminService.java

```java
package top.zhonglinhao.service;

import top.zhonglinhao.entity.Admin;

public interface IAdminService {
	
	public Admin findAll();

}
```

+ AdminService.java

```java
package top.zhonglinhao.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import top.zhonglinhao.entity.Admin;
import top.zhonglinhao.mapper.AdminMapper;
import top.zhonglinhao.service.IAdminService;

@Service
public class AdminService implements IAdminService {

	@Autowired
	private AdminMapper adminMapper;
	
	@Override
	public Admin findAll() {
		return adminMapper.findAll();
	}
}
```
+ TestController.java

```java
package top.zhonglinhao.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import top.zhonglinhao.entity.Admin;
import top.zhonglinhao.service.IAdminService;

@Controller
public class TestController {
	
	@Autowired
	private IAdminService adminService;
	
	@RequestMapping("/hello")
	public String hello() {
		System.out.println("请求到达Controller！");
		Admin admin = adminService.findAll();
		System.out.println(admin.getName() + ":" + admin.getAge());
		return "hello";
	}
}
```
至此，项目整合完毕！

*另外还有资源放行、拦截器、异常处理器等内容，需要的时候配置即可。


-----
ssm框架整合遇到的错误信息：  

+ 因为Controller注入失败而导致报错： *No mapping found for HTTP request with URI [/ssm/hello] in DispatcherServlet with name 'springmvc'*  
+ 因为Mapper在Service层中注入失败而报错：

No qualifying bean of type [top.zhonglinhao.mapper.AdminMapper] found for dependency: expected at least 1 bean which qualifies as autowire candidate for this dependency. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}





