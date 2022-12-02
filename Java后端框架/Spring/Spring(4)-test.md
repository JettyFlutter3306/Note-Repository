# Spring(4)-test

在很多场合下我们都需要在 Spring 上下文环境下进行单元测试, *Junit* 与 Spring 整合只需要几个步骤即可

# 整合JUnit4

1. 导入依赖

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.13.2</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.12.RELEASE</version>
    <scope>test</scope>
</dependency>
```

2. 创建测试类

***@RunWith(SpringRunner.class)*** 指定运行的是 Spring 上下文环境

***@ContextConfiguration("classpath:application.xml")*** 指定配置文件路径

```java
@RunWith(SpringRunner.class)
@ContextConfiguration("classpath:application.xml")
public class TestSpringRunner {
    
    @Autowired
    private UserService userService;
    
    @Test
    public void testJdbcTemplate() {
        List<User> users = userService.selectAll();
        users.forEach(System.out::println);
    }
}
```

# 整合JUnit5

1. 导入依赖

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.7.2</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.12.RELEASE</version>
    <scope>test</scope>
</dependency>
```

2. 创建测试类

***@ExtendWith(SpringExtension.class)*** 指定 Junit 运行的环境

***@ContextConfiguration("classpath:application.xml")*** 指定 Spring 的配置文件位置

```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration("classpath:application.xml")
public class TestJunit5 {

    @Autowired
    private UserService userService;
    
    @Test
    public void testJdbcTemplate() {
        List<User> users = userService.selectAll();
        users.forEach(System.out::println);
    }
}
```

`@Test` 注解导入的是 *org.junit.jupiter.api* 包下的注解，这是 JUnit 5 的注解。