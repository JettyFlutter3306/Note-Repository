# Mybatis(1)

# 一. 入门

## 1.1 原生JDBC弊端

原生 JDBC 实现 CURD 的问题：

1. 编码繁琐。
2. 需要我们自己将结果集映射成对象。
3. 性能不太好。
4. SQL 语句和 Java 代码的耦合度特别高。

## 1.2 认识ORM

JDBC 的缺点：需要手动的完成面向对象的 Java 语言、面向关系的数据库之间数据的转换，代码繁琐无技术含量，影响了开发效率。

查询是需要手动的将结果集的列数据转换为 Java 对象的属性；而添加操作时需要手动将 Java 对象的属性转换为数据库表的列字段。 		

关于面向对象的 Java 语言、面向关系的数据库之间数据的转换必须要做，问题在于这个转换是否可以不由开发者来做。可以的，ORM 框架就是专门来做这个问题的，相当于在面向对象语言和关系数据库之间搭建一个桥梁。

ORM，Object-Relationl Mapping，对象关系映射，它的作用是在关系型数据库和对象之间作一个映射，这样我们在具体的操作数据库的时候，只要像平时操作对象一样操作它就可以了，ORM 框架会根据映射完成对数据库的操作，就不需要再去和复杂的 SQL 语句打交道了。

另外学习 ORM 必须知道两个概念：持久化、持久层：

**持久化**： 

持久(Persistence)，即把数据(如内存中的对象)保存到可永久保存的存储设备中(如磁盘)。持久化的主要应用是将内存中的数据存储在关系型的数据库中，当然也可以存储在磁盘文件中、XML 数据文件中等等。

**持久层**： 

持久层(Persistence Layer)，即专注于实现数据持久化应用领域的某个特定系统的一个逻辑层面，将数据使用者和数据实体相关联。之前使用 JDBC 访问数据库的 DAO 层，后面采用 MyBatis 访问数据库的 mapper 层，就是持久层。

## 1.3 认识Mybatis

Mybatis 是一持久层的款半自动的 ORM 映射框架。

MyBatis 本是 Apache 的一个开源项目 iBatis, 2010 年这个项目由 Apache Software Foundation 迁移到了  Google Code，且改名为 MyBatis 。2013年11月迁移到GitHub。iBATIS 一词来源于 **internet** 和 **abatis** 的组合，是一个基于Java 的持久层框架。 		

MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Ordinary Java Object，普通的 Java 对象)映射成数据库中的记录。

精简解释：MyBatis 是一个半自动 ORM 框架，其本质是对 JDBC 的封装。使用 MyBatis 重点需要程序员编写SQL 命令，不需要写一行 JDBC 代码

# 二. 快速使用

## 2.1 创建工程

打开 IDEA，选择 **New Project**，再选择 Maven 工程，点击确定创建即可。然后导入依赖：

```xml
<dependencies>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.25</version>
    </dependency>

    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>

    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.22</version>
        <scope>provided</scope>
    </dependency>

    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.12</version>
    </dependency>
</dependencies>
```

## 2.2 准备数据库

打开 Navicat Premium，创建数据库表即可。然后在 pojo 包下创建实体类和数据库表字段一一对应，创建部门类 Dept：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class Dept {
    private Integer id;
    private String name;
    private String location;
}
```

## 2.3 创建Mapper映射文件

首先在 *resources* 文件夹下创建 Mybatis 核心配置文件 **sqlMapConfig.xml**，代码如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="jdbc.properties"/>
    
    <settings>
        <setting name="logImpl" value="LOG4J"/>
        <setting name="cacheEnabled" value="true"/>
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="aggressiveLazyLoading" value="true"/>
    </settings>
    
    <!-- 配置实体类别名 -->
    <typeAliases>
        <package name="org.codeArt.pojo"/>
    </typeAliases>
    
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--加载mapper映射文件-->
    <mappers>
<!--        <mapper resource="org/codeArt/mapper/DeptMapper.xml"/>-->
<!--        <mapper class="org.codeArt.mapper.DeptMapper"/>-->
        <package name="org.codeArt.mapper"/>
    </mappers>
</configuration>
```

在 *resources* 文件夹下面创建 mapper 映射文件，映射文件需要放在和源代码同名文件夹中，如：*org/example/DeptMapper.xml*。代码如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="org.codeArt.mapper.DeptMapper">
    <select id="selectAll" resultType="org.codeArt.pojo.Dept">
        select * from `dept`
    </select>
</mapper>
```

## 2.4 运行测试

在 test 文件夹下声明测试类，编写测试方法：

```java
public class TestDept {

    private SqlSession session;
 
    @Before
    public void before() throws IOException {
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        InputStream is = Resources.getResourceAsStream("sqlMapperConfig.xml");
        SqlSessionFactory factory = builder.build(is);
        session = factory.openSession();
    }
    
    @After
    public void after() {
        session.close();
    }
    
    @Test
    public void testSelectAll() {
        // 参数是查询标签的namespace+id，唯一确定一个标签
        List<Dept> list = session.selectList("org.codeArt.mapper.DeptMapper.selectAll");
        list.forEach(System.out::println);
    }
}
```

# 三. 配置详解

## 3.1 日志配置

日志通常使用 log4j 系列的框架，有 1 和 2 两个版本，按需导入即可：

```xml
<!-- log4j2 -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.12.1</version>
</dependency>

<!-- log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

在 *resources* 文件夹创建 log4j.properties 配置文件，将 log4j.properties 文件负责到 src 下。另外在其中可以将全局的日志级别调高，避免大量 debug 信息的干扰。同时将对映射文件的操作调低，可以用来显示 SQL 语句的调试信息。开发阶段，建议启动控制的日志。

```properties
log4j.rootLogger=debug,stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.err
log4j.appender.stdout.layout=org.apache.log4j.SimpleLayout
log4j.appender.logfile=org.apache.log4j.FileAppender
log4j.appender.logfile.File=/log4j.log
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %l %F %p %m%n
```

如果你使用 log4j2，那么就需要使用 xml 配置文件，将 log4j2.xml 文件创建到 *resources* 文件夹下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="DEBUG">
    <Appenders>
        <Console name="Console" target="SYSTEM_ERR">
            <PatternLayout pattern="%d{YYYY-MM-dd HH:mm:ss} [%t] %-5p %c{1}:%L - %msg%n"/>
        </Console>
        <RollingFile name="RollingFile" filename="log/test.log"
                     filepattern="${logPath}/%d{YYYYMMddHHmmss}-fargo.log">
            <PatternLayout pattern="%d{YYYY-MM-dd HH:mm:ss} [%t] %-5p %c{1}:%L - %msg%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="10 MB"/>
            </Policies>
            <DefaultRolloverStrategy max="20"/>
        </RollingFile>
    </Appenders>
    <Loggers>
        <Root level="INFO">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

最后在 *sqlMapConfig.xml* 核心配置文件内，声明如下代码：

```xml
<configuration>
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
</configuration>
```

## 3.2 事务配置

```xml
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
</environments>
```

在 Mybatis 核心配置文件中 envirment 中，通过 transactionManager 标签配置事务的处理策略。

- JDBC：这个配置直接简单使用了 JDBC 的提交和回滚设置。它依赖于从数据源得到的连接来管理事务范围。
- MANAGED：这个配置几乎没做什么。它从来不提交或回滚一个连接。而它会让容器来管理事务的整个生命周期(比如 Spring 或 JavaEE 应用服务器的上下文) 默认情况下它会关闭连接。然而一些容器并不希望这样，因此如果你需要从连接中停止它，将 *closeConnection* 属性设置为 false。Mybatis 本身并不做事务的处理，交给其他框架去处理事务，如 Spring。

## 3.3 映射文件加载方式

Mybatis 的映射文件有如下几种导入方式：

1. 使用文件路径导入，也就是使用 *resource* 属性：

```xml
<mappers>
	<mapper resource="org/codeArt/mapper/DeptMapper.xml"/>
</mappers>
```

2. 使用网络资源路径导入：

```xml
<mappers>
	<mapper url="file:///org/codeArt/mapper/DeptMapper.xml"/>
</mappers>
```

3. 使用接口的全限定名导入，使用的是 mapper 标签的 class 属性(基于接口的代理模式开发)：

```xml
<mappers>
	<mapper class="org.codeArt.mapper.DeptMapper"/>
</mappers>
```

4. 包扫描形式加载所有的 mapper 映射文件，使用的是 package 标签：

```xml
<mappers>
	<package name="org.codeArt.mapper"/>
</mappers>
```

## 3.4 实体类别名配置

在 Mybatis 核心配置文件中使用别名处理：

```xml
<typeAliases>
    <package name="org.codeArt.pojo"/>
</typeAliases>
```

通过包扫描给所有的实体类起别名，给指定报名下的所有类起别名，默认每个实体类的别名是首字母小写的类名。如：

```
Dept -> dept    Emp -> emp
```

在映射文件的 resultType 返回值类型和 paramterType 上就可以使用别名了：

```xml
<select id="selectAll" resultType="emp">
    select * from `emp`
</select>
```

## 3.5 引入外部属性配置文件

在 *resources* 文件夹下创建 *jdbc.properties* 配置文件：

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/test?useSSL=false&useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
username=root
password=root
```

在 *sqlMapConfig* 中引入配置文件：

```xml
<configuration>
    <properties resource="jdbc.properties"/>
</configuration>
```

使用 `${}` 表达式引用外部属性：

```xml
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
</environments>
```

# 四. DAO模式开发







# 五. 代理模式开发

























