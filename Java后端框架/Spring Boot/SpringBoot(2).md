# SpringBoot(2)

# 一. 整合Mybatis

与 Mybatis 整合首先需要导入依赖，版本号父工程里有配置，不需要我们操心：

```xml
<dependency>
   <groupId>com.baomidou</groupId>
   <artifactId>mybatis-plus-boot-starter</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

编写配置文件：

```yml
spring:
  application:
    name: spring-boot-demo
  datasource:
    url: jdbc:mysql://localhost:3306/test?autoReconnect=true&useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root
mybatis:
  type-aliases-package: org.codeArt.pojo  # 别名包扫描
  mapper-locations: classpath:mapper/*.xml
  configuration:
    map-underscore-to-camel-case: on  # 开启下划线转驼峰
```

在启动类上添加 `@MapperScan` 注解表示扫描 mapper 层的接口：

```java
@SpringBootApplication
@MapperScan("ogr.codeArt.mapper")
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class,args);
    }
}
```

如果用了 `@MapperScan` 注解，那么声明 mapper 层接口时就不需要使用 `@Mapper` 注解了：

```java
@Mapper
public interface UserMapper {
    List<User> selectAll();
}
```

在 *resources* 目录下编写 *UserMapper.xml* 映射文件，名称可以不一致，因为是根据 *namespace* 找对应的 Mapper 接口的，但是**最好一致**。

编写 *UserService*：

```java
@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;
    
    @Override
    public List<User> selectAll() {
        return userMapper.selectAll();
    }
}
```

# 二. 整合logback

Spring Boot 默认使用 **Logback** 组件作为日志管理。Logback 是由 log4j 创始人设计的一个开源日志组件。

在 Spring Boot 项目中我们不需要额外的添加 **Logback** 的依赖，因为在 **spring-boot-starter** 或者 **spring-boot-starter-web** 中已经包含了 Logback 的依赖。

Logback 读取配置文件的步骤：

1. 在 classpath 下查找文件 *logback-test.xml*。
2. 如果文件不存在，则查找 *logback.xml*。

配置如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
    <!-- 定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径 -->
    <property name="LOG_HOME" value="${catalina.base}/logs/"/>
    <!-- 控制台输出 -->
    <appender name="Stdout" class="ch.qos.logback.core.ConsoleAppender">
        <!-- 日志输出格式 -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n
            </pattern>
        </layout>
    </appender>
    
    <!-- 按照每天生成日志文件 -->
    <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 日志文件输出的文件名 -->
            <FileNamePattern>${LOG_HOME}/server.%d{yyyy-MM-dd}.log</FileNamePattern>
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>
        
        <layout class="ch.qos.logback.classic.PatternLayout">
            <!-- 格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n
            </pattern>
        </layout>
        
        <!-- 日志文件最大的大小 -->
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <MaxFileSize>10MB</MaxFileSize>
        </triggeringPolicy>
    </appender>
    
    <!-- 日志输出级别 -->
    <root level="info">
        <appender-ref ref="Stdout"/>
        <appender-ref ref="RollingFile"/>
    </root>
    
    <logger name="org.codeArt.mapper" level="DEBUG"/>
    <!-- 日志异步到数据库 -->
    <!--<appender name="DB" class="ch.qos.logback.classic.db.DBAppender">
            日志异步到数据库 
            <connectionSource class="ch.qos.logback.core.db.DriverManagerConnectionSource">
               连接池 
               <dataSource class="com.mchange.v2.c3p0.ComboPooledDataSource">
                  <driverClass>com.mysql.jdbc.Driver</driverClass>
                  <url>jdbc:mysql://127.0.0.1:3306/databaseName</url>
                  <user>root</user>
                  <password>root</password>
                </dataSource>
            </connectionSource>
      </appender> -->
</configuration>
```

# 三. 整合PageHelper

**PageHelper** 插件是一款用于简化分页查询的 SQL 插件。只需要一行代码就可以实现分页。

- 实现原理：

PageHelper 使用了静态的 `ThreadLocal` 参数，分页参数和线程是绑定的。内部流程是 `ThreadLocal` 中设置了分页参数 `pageNum`，`pageSize`，之后在查询执行的时候，获取当前线程中的分页参数，执行查询的时候通过**拦截器**在 sql 语句中添加分页参数，之后实现分页查询，查询结束后在 `finally` 语句中清除 `ThreadLocal` 中的查询参数。

- 使用方式：

首先调用 `PageHelper` 的 *startPage* 方法，传入页码数和页大小参数。然后紧接着调用 Mybatis 的查询方法。只要你可以保证在 `PageHelper` 方法调用后紧跟 Mybatis 查询方法，这就是安全的。因为 `PageHelper` 在 `finally` 代码块中自动清除了 `ThreadLocal` 存储的对象。

导入 **PageHelper** 启动器依赖：

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.3</version>
</dependency>
```

编写 *UserService*：

```java
@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;
    
    public List<User> selectPage(Integer pageNum, Integer pageSize) {
        PageHelper.startPage(pageNum, pageSize);
        return userMapper.selectAll();
    }
}
```

*startPage* 方法返回值是 `Page` 对象，通过 `Page` 对象可以拿到分页体的信息。但是一般不这么做，通常使用 `PageInfo` 对象返回给前端：

```java
public PageInfo<User> selectPage(Integer pageNum, Integer pageSize) {
    Page<User> page = PageHelper.startPage(pageNum, pageSize);
    List<User> list = userMapper.selectAll();
    PageInfo<User> pageInfo = new PageInfo<>(list);
    // System.out.println("当前页：" + pageInfo.getPageNum());
    // System.out.println("总页数：" + pageInfo.getPages());
    // System.out.println("页大小：" + pageInfo.getSize());
    // System.out.println("总记录数：" + pageInfo.getTotal());
    // System.out.println("当前页数据：" + pageInfo.getList());
    return pageInfo;
}
```

`Page` 对象的属性：

```java
private int pageNum;	// 当前页码
private int pageSize; 	// 每页数据的数量
private int startRow;	// 始页首行行号
private int endRow;		// 尾页尾行行号
private long total;		// 总记录数
private int pages;		// 总页数
private Boolean reasonable; // 分页合理化
private Boolean pageSizeZero; // 当设置为true的时候，如果pagesize设置为0（或RowBounds的limit=0），就不执行分页，返回全部结果
```

`PageInfo` 对象属性：

```java
private int pageNum;   			// 当前页
private int pageSize;			// 每页显示数据条数
private int size;				// 当前页的数量
private int startRow; 			// 始页首行行号
private int endRow;				// 尾页尾行行号
private long total;				// 总记录数
private int pages;				// 总页数
private List<T> list;			// 查询结果的数据
private int firstPage;			// 首页
private int prePage;			// 上一页
private int nextPage;			// 下一页
private int lastPage;			// 最后一页
private boolean isFirstPage;	// 是不是第一页
private boolean isLastPage;		// 是不是最后一页
private boolean hasPreviousPage;// 有没有上一页
private boolean hasNextPage;	// 有没有下一页
private int navigatePages;		// 所有导航页号
private int[] navigatepageNums;	// 导航页码数
```

# 四. 整合Druid

Druid 是由阿里巴巴推出的数据库连接池。它结合了 C3P0、DBCP、PROXOOL 等数据库连接池的优点。之所以从众多数据库连接池中脱颖而出，还有一个重要的原因就是它包含控制台，很方便的帮助我们实现对于 sql 执行的监控。

导入依赖，版本不要导入太高，不然控制台页面可能进不去：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.10</version>
</dependency>
```

编写配置文件：

```yml
spring:
  application:
    name: spring-boot-demo
  datasource:
    # 使用阿里的Druid连接池
    type: com.alibaba.druid.pool.DruidDataSource
    url: jdbc:mysql://localhost:3306/test?autoReconnect=true&useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root
    druid:
      # 连接池的配置信息
      # 初始化大小，最小，最大
      initial-size: 5
      min-idle: 5
      maxActive: 20
      # 配置获取连接等待超时的时间
      maxWait: 60000
      # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
      timeBetweenEvictionRunsMillis: 60000
      # 配置一个连接在池中最小生存的时间，单位是毫秒
      minEvictableIdleTimeMillis: 300000
      validationQuery: SELECT 1
      testWhileIdle: true
      testOnBorrow: false
      testOnReturn: false
      # 打开PSCache，并且指定每个连接上PSCache的大小
      poolPreparedStatements: true
      maxPoolPreparedStatementPerConnectionSize: 20
      # 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
      filters: stat,wall,slf4j
      # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
      connectionProperties: druid.stat.mergeSql\=true;druid.stat.slowSqlMillis\=5000
      # 配置DruidStatFilter
      web-stat-filter:
        enabled: true
        url-pattern: "/*"
        exclusions: "*.js,*.gif,*.jpg,*.bmp,*.png,*.css,*.ico,/druid/*"
      # 配置DruidStatViewServlet
      stat-view-servlet:
        url-pattern: "/druid/*"
        # IP白名单(没有配置或者为空，则允许所有访问)
        allow: 127.0.0.1,192.168.8.109
        # IP黑名单 (存在共同时，deny优先于allow)
        deny: 192.168.1.188
        #  禁用HTML页面上的“Reset All”功能
        reset-enable: false
        # 登录名
        login-username: admin
        # 登录密码
        login-password: 123456
```

访问监控页面：

```
http://localhost:8080/druid
```

# 五. 整合JSP

如果使用 Spring Boot 开发，那么基本上不会使用 JSP，这边只做了解即可。

导入依赖：

```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
    <scope>provided</scope>
</dependency>
```

在 *main* 文件夹下创建 *webapp* 目录，然后在项目中 Facets 中配置项目的 Web Application 配置。*webapp* 文件夹有一个蓝点就说明成功了，最后在 *webapp* 目录下创建 *WEB-INF* 目录并且创建 *web.xml* 配置文件。

设置工作目录，如果在 IDEA 中项目结构为聚合工程。那么在运行 JSP 是需要指定路径。如果项目结构为独立项目则不需要。

设置项目的 Working directory 为 `$MODULE_WORKING_DIR$`。

在 *yml* 配置文件中配置视图解析器参数：

```yml
spring:
  mvc:
    view:
      prefix: /WEB-INF/
      suffix: .jsp
```

最后跟 Spring MVC 一样照常使用即可。

# 六. 整合FreeMarker

FreeMarker 是一款模板引擎： 即一种基于模板和要改变的数据， 并用来生成输出文本(HTML网页、电子邮件、配置文件、源代码等)的通用工具。 它不是面向最终用户的，而是一个 Java 类库，是一款程序员可以嵌入他们所开发产品的组件。

FreeMarker 是免费的，基于 Apache 许可证 2.0 版本发布。其模板编写为 FreeMarker Template Language(FTL)，属于简单、专用的语言。需要准备数据在真实编程语言中来显示，比如数据库查询和业务运算，之后模板显示已经准备好的数据。在模板中，主要用于如何展现数据， 而在模板之外注意于要展示什么数据。

常用的java模板引擎还有哪些？

JSP、Freemarker、Thymeleaf 、Velocity 等。模板 + 数据模型 = 输出。Freemarker 并不关心数据的来源，只是根据模板的内容，将数据模型在模板中显示并输出文件(通常为 Html，也可以生成其它格式的文本文件)。

Freemarker 作为 Spring MVC 一种视图格式，默认情况下 Spring MVC 支持 Freemarker 视图格式。 需要创建Spring Boot + Freemarker 工程用于测试模板。

导入依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

编写 Controller：

```java
@Controller
@RequestMapping("/")
public class HelloController {
    @GetMapping("show")
    public String show(Map<String, Object> map) {
        map.put("username", "洛必达");
        return "show";
    }
}
```

在 *resources* 目录下创建 *templates* 目录，然后创建 *show.ftlh* 文件：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
this is first ftlh page
<br/>
${username}
<img src="img/a.jpg"></img>
</body>
</html>
```

其实这里 Freemarker 和 JSP 用起来差不多。

# 七. 整合Thymeleaf

导入依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

关于 Thymeleaf 默认配置：

- `spring.thymeleaf.prefix`：`classpath:/templates/`。
- `spring.thymeleaf.suffix`：`.html`。
