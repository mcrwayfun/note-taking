# spring boot 2.x快速学习笔记
* [springboot文档](#1-springboot文档)
* [基本环境](#2-基本环境)
* [快速入门](#3-快速入门)
* [文件上传](#4-文件上传)
* [项目打包jar](#5-项目打包jar)
* [热部署](#6-热部署)
* [配置文件](#7-配置文件)
* [单元测试](#8-单元测试)
* [全局异常](#9-全局异常)
* [打包成war](#10-打包成war)
* [过滤器和拦截器](#11-过滤器和拦截器)
* [模板引擎](#12-模板引擎)
* [mybatis](#13-mybatis)
* [日志](#14-日志)

## 1. springboot文档
1. springboot GitHub地址：https://github.com/spring-projects/spring-boot
2. springboot 官方文档：https://spring.io/guides/gs/spring-boot/
3. 手动快速创建一个spring boot项目：http://spring.io/guides/gs/rest-service/
4. 项目结构推荐：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-using-the-default-package
5. 使用构建工具自动生成项目基本架构：https://start.spring.io/
6. SpringBoot2.x的依赖默认Maven版本：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#appendix-dependency-versions
7. 静态资源默认配置：https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-developing-web-applications.html#boot-features-spring-mvc-static-content
8. application.properties配置信息：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#common-application-properties
9. banner：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-banners
10. jar和war目录讲解：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#executable-jar-jar-file-structure
11. jetty,tomcat,undertow性能比较：https://examples.javacodegeeks.com/enterprise-java/spring/tomcat-vs-jetty-vs-undertow-comparison-of-spring-boot-embedded-servlet-containers/
12. filter：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-embedded-container-servlets-filters-listeners
13. starter：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#using-boot-starter
14. thymeleaf官网：https://www.thymeleaf.org/doc/articles/thymeleaf3migration.html
15. thymeleaf快速入门：https://www.thymeleaf.org/doc/articles/standarddialect5minutes.html
16. mybatis官网：http://www.mybatis.org/mybatis-3/zh/java-api.html
17. mybatis-spring-boot-autoconfigure：http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/#Configuration
18. mybatis-spring-boot-sample：https://github.com/mybatis/spring-boot-starter/tree/master/mybatis-spring-boot-samples
19. spring-boot日志：https://docs.spring.io/spring-boot/docs/2.1.0.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-logging
20. logback：https://logback.qos.ch/manual/index.html

## 2. 基本环境
- jdk1.8+
- maven3.2+
- 任何一款ide

## 3. 快速入门
### 3.1 官方推荐包命名接口
```java
			com
			 +- example
			     +- myapplication
			         +- Application.java
			         |
			         +- customer
			         |   +- Customer.java
			         |   +- CustomerController.java
			         |   +- CustomerService.java
			         |   +- CustomerRepository.java
			         |
			         +- order
			             +- Order.java
			             +- OrderController.java
			             +- OrderService.java
			             +- OrderRepository.java
```
### 3.2 目录文件结构
```java
		 src/main/java：存放代码
		 src/main/resources
		 	static: 存放静态文件，比如 css、js、image, （访问方式 http://localhost:8080/js/main.js）
		 	templates:存放静态页面jsp,html,tpl
		 	config:存放配置文件,application.properties
		 	resources:其他
```
### 3.3 静态资源文件加载顺序
对于同一个文件，springboot默认会挨个从**META/resources > resources > static > public**里面找是否存在相应的资源，如果有则直接返回。

### 3.4 访问html
想要访问html，有两种做法：
1. url中直接直接访问html：localhost:8080/index.html。访问顺序为[3.3](#33-静态资源文件加载顺序)所示。如果访问失败，则说明index.html不在静态资源加载文件中。可以在application.properties中使用**spring.resources.static-locations**来进行配置
2. 添加模板引擎的依赖，通过requestMapping来访问，且index.html要放在src/main/resources/templates文件夹下
```java
		<dependency>
		   <groupId>org.springframework.boot</groupId>
		   <artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
```

## 4. 文件上传
1. MultipartFile 对象的transferTo方法，用于文件保存（效率和操作比原先用FileOutStream方便和高效）
2. 可以使用配置的方式或者@Bean的方式来添加上传文件的配置
```java
@Bean  
public MultipartConfigElement multipartConfigElement() {  
	MultipartConfigFactory factory = new MultipartConfigFactory();  
	//单个文件最大  
	factory.setMaxFileSize("10240KB"); //KB,MB  
	/// 设置总上传数据总大小  
	factory.setMaxRequestSize("1024000KB");  
	return factory.createMultipartConfig();  
}
```
3. 当访问盘符中的文件时，需要在**spring.resources.static-locations**后添加路径
```java
web.upload-path=H:\\temp\\
spring.resources.static-locations=file:${web.upload-path}
```
使用@Value的方式使用web.upload-path
```java
@Value("${web.upload-path}")
private String filePath;
```

## 5. 项目打包jar
打包成jar包，需要增加maven依赖,如果没加相关依赖，执行maven打包，运行后会报错:no main manifest attribute, in XXX.jar
```java
		<build>
			<plugins>
				<plugin>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-maven-plugin</artifactId>
				</plugin>
			</plugins>
		</build>
```

## 6. 热部署
1. 添加依赖包，修改后会重启应用，立即生效。生产环境不要开启这个功能，如果用java -jar启动，springBoot是不会进行热部署的
```
		<dependency>  
		     <groupId>org.springframework.boot</groupId>  
		     <artifactId>spring-boot-devtools</artifactId>  
		     <optional>true</optional>  
		 </dependency>
```
2. 不会被热部署的文件
(a)/META-INF/maven, /META-INF/resources, /resources, /static, /public, or /templates
(b)指定文件不进行热部署 spring.devtools.restart.exclude=static/**

3. 手动触发重启：使用一个文件来控制。
在application.properties中添加配置`spring.devtools.restart.trigger-file=trigger.txt`，trigger.txt放在`src/main/resources/`下即可，通过改变version的值来重启项目
4. idea中热部署不生效

因为idea把修改类自动编译给关闭了，所以需要我们手动开启

![](https://i.imgur.com/hk4jKeJ.png)

然后 Shift+Ctrl+Alt+/，选择Registry

![](https://i.imgur.com/Zbcv1iL.png)

进去之后，找到如下图所示的选项，打勾

![](https://i.imgur.com/Oc6xVSe.png)
## 7. 配置文件
SpringBoot2.x常见的配置文件 xml、yml、properties的区别和使用
1. 常见的配置文件 xx.yml, xx.properties，
(a)YAML（Yet Another Markup Language）
写 YAML 要比写 XML 快得多(无需关注标签或引号)，使用空格 Space 缩进表示分层，不同层次之间的缩进可以使用不同的空格数目。注意：**key后面的冒号，后面一定要跟一个空格,树状结构**，application.properties示例
```java
server.port=8090  
server.tomcat.max-threads=0  
server.tomcat.uri-encoding=UTF-8 
```
application.yml示例
```java
server:  
	port: 8090  
	session-timeout: 30  
	tomcat.max-threads: 0  
	tomcat.uri-encoding: UTF-8 
```
2. 默认示例文件仅作为指导。 不要将整个内容复制并粘贴到您的应用程序中，只挑选您需要的属性。
3. 使用@Value将配置自动映射到属性
有两个配置属性
```java
test.domain=www.xdclass.net
test.name=springboot
```
引用
```java
// 在controller上添加注解
@PropertySource({"classpath:resource.properties"})
// 增加属性
@Value("${test.name}")
private String name;
```
实体类中配置，通常用于配置类
(1)使用@Value("${}")即可获取application.properties配置中的属性
```java
//服务器配置
@Component
@PropertySource({"classpath:application.properties"})
public class ServerSettings {
	//名称
	@Value("${test.name}")
	private String name;
	@Value("${test.domain}")
	private String domain;
}
```
(2)使用注解`@ConfigurationProperties`（若有前缀需要则需要制定），可以将配置文件中同属性名的配置自动注入到对应的属性中。
```java
//服务器配置
@Component
@PropertySource({"classpath:application.properties"})
@ConfigurationProperties(prefix = "test")
public class ServerSettings {
	//名称
	private String name;
	private String domain;
}
```

## 8. 单元测试
引入相关依赖
```
<!--springboot程序测试依赖，如果是自动创建项目默认添加-->
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
		<scope>test</scope>
</dependency>
```
测试类添加注解
```java
@RunWith(SpringRunner.class)  //底层用junit  SpringJUnit4ClassRunner
@SpringBootTest(classes={Application.class})//启动整个springboot工程
public class SpringBootTests { }
```
可以使用注解`@AutoConfigureMockMvc`来实现mock的测试

## 9. 全局异常
使用类注解`@ControllerAdvice`或者`@RestControllerAdvice`就可以捕获到未处理的异常。可以返回一个json串，由前端人员决定跳转到错误的界面。亦或者返回一个页面
```java
//捕获全局异常,处理所有不可知的异常
@ExceptionHandler(value=Exception.class)
Object handleException(Exception e,HttpServletRequest request){
	LOG.error("url {}, msg {}",request.getRequestURL(), e.getMessage()); 
	Map<String, Object> map = new HashMap<>();
		map.put("code", 100);
		map.put("msg", e.getMessage());
		map.put("url", request.getRequestURL());
		return map;
}

@ExceptionHandler(value=MyException.class)
Object handleMyException(MyException e,HttpServletRequest request){
	//进行页面跳转
	ModelAndView modelAndView = new ModelAndView();
	modelAndView.setViewName("error.html");
	modelAndView.addObject("msg", e.getMessage());
	return modelAndView; 
}
```

## 10. 打包成war
(1)在pom.xml中将打包形式jar修改为war<packaging>war</packaging>

(2)构建项目名称 <finalName>demo</finalName>
```java
	<build>
		<finalName>demo</finalName>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
```
(3)修改启动类
```java
public class XdclassApplication extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
		return application.sources(XdclassApplication.class);
	}

	public static void main(String[] args) throws Exception {
		SpringApplication.run(XdclassApplication.class, args);
	}

}
```
(4)打包项目，启动tomcat

**springboot2.x要使用tomcat8**

## 11. 过滤器和拦截器
### 11.1 使用Servlet3.0配置自定义Filter
springboot启动时默认加载的filter（characterEncodingFilter、hiddenHttpMethodFilter、httpPutFormContentFilter、requestContextFilter）

新建一个Filter类，implements Filter，并实现对应的接口 --》 @WebFilter 标记一个类为filter，被spring进行扫描 --》 控制chain.doFilter的方法的调用，来实现是否通过放行 --》 启动类里面增加 @ServletComponentScan，进行扫描

### 11.2 使用Servlet3.0的注解自定义原生Servlet
创建一个Servlet类，extends HttpServlet，并实现对应的接口 --》 @WebServlet(name = "userServlet",urlPatterns = "/test/customs")标记一个类为servlet，urlPatterns设置匹配路径 --》 启动类里面增加 @ServletComponentScan，进行扫描

### 11.3 使用Servlet3.0的注解自定义原生Listener监听器
常用监听器有（servletContextListener、httpSessionListener、servletRequestListener）

创建一个listener类，implements ServletRequestListener接口（其他也行），并实现对应的接口方法 --》 @WebListener标记为一个listener --》启动类里面增加 @ServletComponentScan，进行扫描

### 11.4 自定义拦截器及新旧配置对比
springboot2.x以前：创建一个类CustomOldWebMvcConfigurer，extends WebMvcConfigurerAdapter，并继承addInterceptors方法 **--》**使用@Configuration标记为一个拦截器 **--》**编写拦截器LoginIntercepter，implements HandlerInterceptor，重写三个方法

springboot2.x：创建一个类CustomWebMvcConfigurer，implements WebMvcConfigurer，实现默认方法addInterceptors --》**使用@Configuration标记为一个拦截器 **--》**编写拦截器LoginIntercepter，implements HandlerInterceptor，重写三个方法

HandlerInterceptor的3个方法：（**preHandle**：调用Controller某个方法之前;**postHandle**：Controller之后调用，视图渲染之前，如果控制器Controller出现了异常，则不会执行此方法;**afterCompletion**：不管有没有异常，这个afterCompletion都会被调用，用于资源清理）

拦截器按照注册的顺序进行拦截，先注册，先拦截

拦截器不生效的原因：
- 是否有加@Configuration；
- 拦截路径是否有问题 **  和 * ；
- 拦截器最后路径一定要 “/**”， 如果是目录的话则是 /*/

### 11.5 Filter和Interceptor的不同
- Filter是基于函数回调doFilter()，而Interceptor则是基于AOP思想
- Filter在只在Servlet前后起作用，而Interceptor够深入到方法前后、异常抛出前后等
- 依赖于Servlet容器即web应用中，而Interceptor不依赖于Servlet容器所以可以运行在多种环境。
- 在接口调用的生命周期里，Interceptor可以被多次调用，而Filter只能在容器初始化时调用一次。
- Filter和Interceptor的执行顺序：过滤前->拦截前->action执行->拦截后->过滤后

## 12. 模板引擎
### 12.1 常见模板的对比
(a)、JSP（后端渲染，消耗性能）

Java Server Pages 动态网页技术,由应用服务器中的JSP引擎来编译和执行，再将生成的整个页面返回给客户端。可以写java代码，springboot不推荐。

(b)、Freemarker 

FreeMarker Template Language（FTL），文件一般保存为 xxx.ftl。严格依赖MVC模式，不依赖Servlet容器（不占用JVM内存），内建函数

(c)、Thymeleaf (主推)

轻量级的模板引擎（负责逻辑业务的不推荐，解析DOM或者XML会占用多的内存）。可以直接在浏览器中打开且正确显示模板页面。直接是html结尾，直接编辑

### 12.2 整合Freemarker
引入依赖
```java
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>
```
引入配置文件
```java
		# 是否开启thymeleaf缓存,本地为false，生产建议为true
		spring.freemarker.cache=false

		spring.freemarker.charset=UTF-8
		spring.freemarker.allow-request-override=false
		spring.freemarker.check-template-location=true
		
		#类型
		spring.freemarker.content-type=text/html

		spring.freemarker.expose-request-attributes=true
		spring.freemarker.expose-session-attributes=true
		
		#文件后缀
		spring.freemarker.suffix=.ftl
		#路径
		spring.freemarker.template-loader-path=classpath:/templates/
```

### 12.3整合Thymeleaf
引入依赖
```java
		<dependency>
		   	<groupId>org.springframework.boot</groupId>
		   	<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
```
引入配置文件
```java
		#开发时关闭缓存,不然没法看到实时页面
		spring.thymeleaf.cache=false
		spring.thymeleaf.mode=HTML5
		#前缀
		spring.thymeleaf.prefix=classpath:/templates/
		#编码
		spring.thymeleaf.encoding=UTF-8
		#类型
		spring.thymeleaf.content-type=text/html
		#名称的后缀
		spring.thymeleaf.suffix=.html
```

## 13. mybatis
引入依赖
```java
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.2</version>
			<scope>runtime</scope>			    
		</dependency>

		<!-- MySQL的JDBC驱动包	-->	
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency> 
		<!-- 引入第三方数据源 -->		
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.1.6</version>
		</dependency>
```
加入配置文件
```java
		#mybatis.type-aliases-package=net.xdclass.base_project.domain
		#可以自动识别
		#spring.datasource.driver-class-name =com.mysql.jdbc.Driver

		spring.datasource.url=jdbc:mysql://localhost:3306/movie?useUnicode=true&characterEncoding=utf-8
		spring.datasource.username =root
		spring.datasource.password =password
		#如果不使用默认的数据源 （com.zaxxer.hikari.HikariDataSource）
		spring.datasource.type =com.alibaba.druid.pool.DruidDataSource

		#增加打印sql语句，一般用于本地开发测试
		mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```
加载配置，注入到sqlSessionFactory等都是springBoot帮我们完成

启动类增加mapper扫描

@MapperScan("com.qingtian.mapper")

常用crud
```java
	    @Select("SELECT * FROM user")
	    @Results({
	        @Result(column = "create_time",property = "createTime")  //javaType = java.util.Date.class        
	    })
	    List<User> getAll();
	  
	    @Select("SELECT * FROM user WHERE id = #{id}")
	    @Results({
	    	 @Result(column = "create_time",property = "createTime")
	    })
	    User findById(Long id);

	    @Update("UPDATE user SET name=#{name} WHERE id =#{id}")
	    void update(User user);

	    @Delete("DELETE FROM user WHERE id =#{userId}")
	    void delete(Long userId);
```

## 14. 日志
### 14.1 logback介绍
基于Log4j基础上大量改良，不能单独使用，推荐配合日志框架SLF4J来使用

logback当前分成三个模块：logback-core,logback-classic和logback-access;logback-core是其它两个模块的基础模块

### 14.2 Logback的核心对象
- Logger：日志记录器
- Appender：指定日志输出的目的地，目的地可以是控制台，文件
- Layout：日志布局 格式化日志信息的输出

### 14.3 日志级别：DEBUG < INFO < WARN < ERROR
```
		===========log4j示例===========		
		 ### 设置###
		log4j.rootLogger = debug,stdout,D,E

		### 输出信息到控制抬 ###
		log4j.appender.stdout = org.apache.log4j.ConsoleAppender
		log4j.appender.stdout.Target = System.out
		log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
		log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n

		### 输出DEBUG 级别以上的日志到=D://logs/error.log ###
		log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
		log4j.appender.D.File = D://logs/log.log
		log4j.appender.D.Append = true
		log4j.appender.D.Threshold = DEBUG 
		log4j.appender.D.layout = org.apache.log4j.PatternLayout
		log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

		### 输出ERROR 级别以上的日志到=D://logs/error.log ###
		log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
		log4j.appender.E.File =E://logs/error.log 
		log4j.appender.E.Append = true
		log4j.appender.E.Threshold = ERROR 
		log4j.appender.E.layout = org.apache.log4j.PatternLayout
		log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n 
```
### 14.4 Log4j日志转换为logback在线工具
支持log4j.properties转换为logback.xml,不支持 log4j.xml转换为logback.xml,https://logback.qos.ch/translator/

### 14.5 springboot整合logback
默认情况下，SpringBoot将日志输出到控制台

创建日志文件logback-spring.xml，官方推荐**-spring.xml**结尾。默认加载加载配置顺序logback-spring.xml，logback-spring.groovy，logback.xml，logback.groovy

logback-spring.xml配置有如下节点：
root节点要加在最后面

```
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
	 <!-- 输出日志到控制台，默认输出debug -->
	 <appender name="consoleApp" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>
                %date{yyyy-MM-dd HH:mm:ss.SSS} %-5level[%thread]%logger{56}.%method:%L -%msg%n
            </pattern>
        </layout>
    </appender>
	<!-- 输出到文件中 -->
    <appender name="fileInfoApp" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<!-- 过滤器，遇到ERROR级别的日志，onMath命中则DENY，onMisMatch不命中则ACCEPT -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
             <level>ERROR</level>
            <onMatch>DENY</onMatch>
            <onMismatch>ACCEPT</onMismatch>
        </filter>
		<!-- 输出格式 -->
        <encoder>
            <pattern>
                %date{yyyy-MM-dd HH:mm:ss.SSS} %-5level[%thread]%logger{56}.%method:%L -%msg%n
            </pattern>
        </encoder>
        <!-- 滚动策略 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 路径 -->
            <fileNamePattern>app_log/log/app.info.%d.log</fileNamePattern>
        </rollingPolicy>
    </appender>
    <appender name="fileErrorApp" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<!-- 日志级别低于ERROR不输出 -->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>ERROR</level>
        </filter>
        <encoder>
            <pattern>
                %date{yyyy-MM-dd HH:mm:ss.SSS} %-5level[%thread]%logger{56}.%method:%L -%msg%n
            </pattern>
        </encoder>
        
        <!-- 设置滚动策略 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 路径 -->
            <fileNamePattern>app_log/log/app.err.%d.log</fileNamePattern>
            
            <!-- 控制保留的归档文件的最大数量，超出数量就删除旧文件，假设设置每个月滚动，
            且<maxHistory> 是1，则只保存最近1个月的文件，删除之前的旧文件 -->
             <MaxHistory>1</MaxHistory>
            
        </rollingPolicy>
    </appender>
    <root level="INFO">  
        <appender-ref ref="consoleApp"/>
        <appender-ref ref="fileInfoApp"/>
        <appender-ref ref="fileErrorApp"/>
    </root>
</configuration>
```