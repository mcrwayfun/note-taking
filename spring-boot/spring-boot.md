# spring-boot
## spring-boot测试
1. 使用spring-boot进行测试，大多数情况下只需引用`spring-boot-starter-test`就能够满足。
2. `spring-boot-starter-test`提供以下的库
	- `JUnit`
	- `Spring Test`&`Spring Boot Test`
	- `AssertJ`
	- `Hamcrest` 一个匹配对象库 
	- `Mockito` 一个Java模拟框架
	- `JSONassert` 一个针对JSON的断言库
	- `JsonPath` 用于JSON的Xpath
3. Spring Boot提供一个`@SpringApplicationConfiguration`注解用于替换标准的spring test的`@ContextConfiguration`,其工作方式时通过SpringApplication创建用于测试的ApplicationContext。
4. 使用`@SpringBootTest`的`webEnvironment`属性定义怎么运行测试：
	- MOCK 加载`WebApplicationContext`,提供一个mock servlet的环境、使用该注解时内嵌servlet容器不会启动。如果classpath下不存在servlet APIs，那么该模式会创建一个常规的non-web `ApplicationContext`。
	- `RANDOM_PORT` 加载`EmbeddedWebApplicationContext`,并提供一个真实的servlet环境。使用该模式内嵌容器将启动，并监听一个随机端口。
	- `DEFINED_PORT` 加载`EmbeddedWebApplicationContext`，并提供一个真实的servlet环境。使用该模式内嵌容器将启动，并监听一个定义好的端口（比如`application,porperties`中定义的或默认的`8080`端口）。
	- `NONE` 使用`SpringApplication`加载一个`ApplicationContext`,但不提供任何servlet环境。
5. 使用测试用例要添加`@RunWith(SpringRunner.class)`,否则该注解将被忽略。  
6. 发现测试配置：SpringBoot使用`@*Test`注解来自动搜索主配置类。搜索算法从包含测试类的package开始搜索，直到发现`@SpringBootApplication`或`@SpringBootConfiguration`注解的类。
7. 使用自定义主配置类，可以使用一个内嵌的`@TestConfiguration`,它不会像Spring内嵌的`@Configuration`类（会替换应用的主配置类），内嵌的`@TestConfiguration`类可以和应用配置类一起使用。这里要注意冲突的问题。
8. Spring测试框架会缓存应用上下文，所以测试共享的相同配置，加载上下文的潜在时间消耗只会发生一次。（使用内嵌`@TestConfiguration`时注意即可）
9. 排除测试配置：在测试时并不需要扫描所有的组件或配置，可以使用`@TestComponet`和`@TestConfiguration`注解，可以用在`src/test/java`目录下的类。加了以上两个注解，则不会被扫描。如果在测试类中（任何有`@Test`或`@RunWith`注解的类）中定义`@Configuration`或`@Component`内部类，则会自动被过滤，使用`@TestConfiguration`则不会。
10. 如果使用的是`@ComponentScan`进行扫描而不是`@SpringBootApplication`,则需要为其注册`TypeExcludeFilter`,参考[Javadoc](https://docs.spring.io/spring-boot/docs/1.4.1.RELEASE/api/org/springframework/boot/context/TypeExcludeFilter.html)
11. `@LocalServerPort`注解可以用来获取测试用例实际使用端口，for example:
```
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)
public class MyWebIntegrationTests {

    @Autowired
    EmbeddedWebApplicationContext server;

    @LocalServerPort
    int port;

    // ...
}
```
12. 可以使用`@Autowired`来加载`TestRestTemplate`发起REST调用。
13.	`@MockBean`注解可用于为ApplicationContext中的bean定义一个Mockito mock，可以使用该注解添加新的beans。，可以用于测试类和测试类字段，或者用于`@Configuration` 注解的类和字段。
14. 自动配置测试？？？？`	spring-boot-test-autoconfigure`
15. `ConfigFileApplicationContextInitializer`是一个`ApplicationContextInitializer`，可以在测试类中加载Spring Boot的`application.porperties`,for example:  
```
@ContextConfiguration(classes=Config.class,initializers=ConfigFileApplicationContextInitializer.class)
``` 
16. 在集成测试中，可以使用`TestRestTemplate`来获取一个普通的或发送基本HTTP认证（使用用户名和密码）的模板：不允许重定向（这样才可以对相应地址进行断言），忽略cookies（模板本身就是无状态的），对于服务出错不会抛出异常。

	如果使用了`@SpringBootTest`，且配置了`WebEnvironment.RANDOM_PORT`或`WebEnvironment.DEFINED_PORT`属性，就可以使用`@Autowired`来加载`TestRestTemplate`发起REST调用。如果有需要，还可以通过`RestTemplateBuilder bean`来进行额外的自定义，for example:
```
@RunWith(SpringRunner.class)
    @SpringBootTest
    public class MyTest {
        @Autowired
        private TestRestTemplate template;

        @Test
        public void testRequest() throws Exception {
            HttpHeaders headers = template.getForEntity("http://myho st.com", String.class).getHeaders();
            assertThat(headers.getLocation().toString(), containsStr ing("myotherhost"));
        }

        @TestConfiguration
        static class Config {
            @Bean
            public RestTemplateBuilder restTemplateBuilder() {
                return new RestTemplateBuilder().additionalMessageConverters(...)																.
                customizers(...);
            }
        }
    }
```
代码还展示了如何使用`@TestConfiguration`加载配置内部类。

## spring-boot关系型数据库
1. Spring为使用SQL数据库提供了广泛的支持，从使用`JdbcTemplate`直接访问JDBC到完全的'对象关系映射'技术，比如Hibernate，Mybatis。Spring Data更是提供了从接口创建`Repository`实现，并根据约定从方法生成查询。
	1. java的`javax.sql.DataSource`提供了使用数据库连接的方法，通常DataSource使用`URL`和相应的参数去初始化数据库连接。
	2. 可以使用内嵌型数据库（但不支持持久化），不需要提供任何的`URL`，比如spring支持的h2，HSQL。需要添加spring-jdbc的依赖，而spring-boot-starter-data-jpa已经包含该依赖了。如出于某种原因需要配置内嵌数据库的URL，要确保数据库的自动连接是关闭的，交由Spring Boot来控制何时关闭数据库。
