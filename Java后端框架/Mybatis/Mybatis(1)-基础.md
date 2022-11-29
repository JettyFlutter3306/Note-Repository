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

普通模式，也称为传统 DAO 模式，就是在传统 DAO 模式下，定义接口和实现类，如：

```java
public interface EmpDao { }
class EmpDaoImpl implements EmpDao { }
```

在实现类中，用 SqlSession 对象调用 *select*、*insert*、*delete*、*update* 等方法实现。目前极为少见，在传统模式下，我们需要知道 SqlSession 对象实现 CRUD 和参数传递的处理。

## 4.1 查询的三种方式

SqlSession 类内置了三个用于查询数据库的 API：

- **selectOne**：查询单个对象。
- **selectList**：将查询的结果封装成 List 集合。
- **selectMap**：将查询的结果封装成 Map 集合，Key 通常设为对象的主键。

声明 Emp 实体类：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class Emp {
    private Integer id;
    private String name;
    private String job;
    private Integer mgr;
    private Date hireDate;
    private Double sal;
    private Double comm;
    private Integer deptId;
}
```

编写 *EmpMapper.xml* 映射文件，是用 *select* 标签编写 SQL 语句，表名可以用飘号包裹起来以防和保留字冲突：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="org.codeArt.mapper.EmpMapper">
    <select id="selectOne" resultType="org.codeArt.pojo.Emp">
        select * from `emp`
        where id = 7499
    </select>

    <select id="selectAll" resultType="org.codeArt.pojo.Emp">
        select * from `emp`
    </select>

    <select id="selectMap" resultType="org.codeArt.pojo.Emp">
        select * from `emp`
    </select>
</mapper>
```

在 *sqlMapConfig.xml* 中导入 *EmpMapper.xml* 映射文件：

```xml
<mappers>
    <mapper resource="org/codeArt/mapper/EmpMapper.xml"/>
</mappers>
```

创建测试类 *TestEmp*：

```java
public class TestEmp {

    private SqlSession session;

    @Before
    public void before() throws IOException {
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        InputStream is = Resources.getResourceAsStream("sqlMapperConfig.xml");
        SqlSessionFactory factory = builder.build(is);
        // 设置自动提交事务
        session = factory.openSession(true);
    }

    @After
    public void after() {
        session.close();
    }
    
    @Test
    public void testSelectOne() {
        Emp emp = session.selectOne("selectOne");
        System.out.println(emp);
    }

    @Test
    public void testSelectAll() {
        List<Emp> list = session.selectList("org.codeArt.mapper.EmpMapper.selectAll");
        list.forEach(System.out::println);
    }

    @Test
    public void testSelectMap() {
        // selectMap第二个参数需要指定Key的值，这里设置了实体类的id
        Map<Integer, Emp> map = session.selectMap("org.codeArt.mapper.EmpMapper.selectMap", "id");
        for (Map.Entry<Integer, Emp> entry : map.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }  
}
```

使用 SqlSession 查询结果需要指定 *select* 标签的 id 的命名空间，因为假如你在别的 Mapper 文件中声明了同样的 id 那么就会冲突，加上 namespace 就不会冲突。

## 4.2 传递参数的方式

当你编写动态 SQL 时，那么就需要传递参数。

### 4.2.1 单个基础数据类型

参数为单个基本数据类型时，可以使用 `#{}` 或者 `${}` 作为占位符表示传递进来的参数，**大括号内可以随便写**，Mybatis 会自动判断，但是最好见名知意：

```xml
<select id="selectOne" resultType="org.codeArt.pojo.Emp" parameterType="int">
    select * from `emp`
    where id = #{id}
</select>
```

*parameterType* 即使不写，Mybatis 也会自动判断。*parameterType* 表示参数的类型。

`#{}` 占位符表示 Mybatis 底层使用 PreparedStatement 对象，然后将占位符替换为 `?`，进行一个预处理操作，可以防止 SQL 注入风险，这个最常用。

`${}` 占位符表示 Mybatis 底层使用 Statement 对象，然后直接使用字符串拼接 SQL 语句，没有预处理操作，不能防止 SQL 注入。`#{}` 可以做的参数映射操作，`${}` 同样可以做。

### 4.2.2 多个基础数据类型

假如传入多个基本数据类型，包括 String，那么可以使用以下几种方式传递参数：

1. `@Param` 注解：

定义在 DAO 层接口，相当于给参数起别名，在 mapper 文件中，使用 `#{}` 做一个映射即可。

```java
public interface EmpMapper {
    Emp selectByNameAndId(@Param("name") String name, @Param("id") Integer id);
}
```

```xml
<select id="selectByNameAndId" resultType="org.codeArt.pojo.Emp">
    select * from `emp`
    where name = #{name}
    and id = #{id}
</select>
```

假如 `@Param` 注解的值和参数名一样，那么就可以省略注解。

2. 使用 `arg` 预置标识符：

`arg` 标识符表示从参数列表索引 0 开始计数，`arg0` 表示索引 0 的参数，`arg1` 表示索引 1 的参数。

```xml
<select id="selectByNameAndId" resultType="org.codeArt.pojo.Emp">
    select * from `emp`
    where name = #{arg0}
    and id = #{arg1}
</select>
```

3. 使用 `param` 预置标识符：

`param` 标识符表示从参数顺序 1 开始计数，`param1` 表示第一个参数，`param2` 表示第二个参数。

```xml
<select id="selectByNameAndId" resultType="org.codeArt.pojo.Emp">
    select * from `emp`
    where name = #{param1}
    and id = #{param2}
</select>
```

### 4.2.3 Map集合作参数

Map 集合也可以作为参数，使用 `#{}` 与 Map 集合的 Key 做映射即可：

```java
public interface EmpMapper {
    List<Emp> selectByCondition(Map<String, Object> condition);
}
```

`#{}` 表示 Key，映射为 Value：

```xml
<select id="selectByCondition" resultType="org.codeArt.pojo.Emp">
    select * from `emp`
    where name = #{name}
    and id = #{id}
</select>
```

### 4.2.4 引用类型作参数

普通的 POJO 作为参数也是跟 Map 一样，使用 `#{}` 作为属性映射成值即可。

```java
public interface EmpMapper {
    List<Emp> selectByCondition(Emp emp);
}
```

`#{}` 在这里表示实体类的属性名称，映射成设定的值。

```xml
<select id="selectByCondition" resultType="org.codeArt.pojo.Emp">
    select * from `emp`
    where name = #{name}
    and id = #{id}
</select>
```

## 4.3 完成DML操作

要完成增删改操作，那么只需要在 Mapper 映射文件中使用 *insert*、*delete*、*update* 标签即可：

```xml
<mapper namespace="org.codeArt.mapper.EmpMapper">
    <insert id="insertOne">
        insert into
            `emp`
        values (default, #{name}, #{job}, #{mgr}, #{hireDate}, #{sal}, #{comm}, #{deptId})
    </insert>
    
    <update id="updateOne">
        update
            `emp`
        set
            name = #{name},
            sal = #{sal}
        where
            id = #{id}
    </update>
    
    <delete id="deleteById">
        delete from `emp` where id = #{id}
    </delete>
</mapper>
```

编写测试方法：

```java
@Test
public void testInsertOne() {
    Emp emp = new Emp();
    emp.setName("洛必达").setDeptId(20).setHireDate(new Date()).setMgr(7559)
        .setComm(1000.0).setJob("Programmer").setSal(10000.0);
    int i = session.insert("org.codeArt.mapper.EmpMapper.insertOne", emp);
    System.out.println(i);
}

@Test
public void testUpdateOne() {
    Emp emp = new Emp();
    emp.setId(1009).setName("曹操").setSal(88888.0);
    int i = session.update("org.codeArt.mapper.EmpMapper.updateOne", emp);
    System.out.println(i);
}

@Test
public void testDeleteById() {
    int i = session.delete("org.codeArt.mapper.EmpMapper.deleteById", 1009);
    System.out.println(i);
}
```

# 五. 代理模式开发

前面已经使用 MyBatis 完成了对 Emp 表的 CRUD 操作，都是由 SqlSession 调用自身方法发送 SQL 命令并得到结果的，实现了 MyBatis 的入门。

但是却存在如下缺点：

1. 不管是 *selectList*、*selectOne*、*selectMap*，都是通过 SqlSession 对象的 API 完成增删改查，都只能提供一个查询参数。如果要多个参数，需要封装到 JavaBean 或者 Map 中，并不一定永远是一个好办法。
2. 返回值类型较固定。
3. 只提供了映射文件，没有提供数据库操作的接口，不利于后期的维护扩展。

在 MyBatis 中提供了另外一种成为 **Mapper代理（或称为接口绑定）** 的操作方式。在实际开发中也使用该方式。下面我们就是要 Mapper 代理的方式来实现对 Emp 表的 CRUD 操作吧，还有完成多个参数传递、模糊查询、自增主键回填等更多的技能实现。搭建好的项目框架如图所示，相比而言，增加了接口 EmpMapper。但是却会引起映射文件和测试类的变化。

主要有以下优点：

1. 有接口模块之间有规范了。
2. 参数的处理多样了，接口中的方法参数列表由我们自己决定。
3. 通过代理模式由 Mybatis 提供接口的实现类对象，我们不用写实现类了。

声明 EmpMapper 接口，mapper 层的接口需要和 Mapper XML 映射文件放在同一层，映射文件在 *resources* 文件夹下的路径要和对应的接口包路径一样。如：*org/mybatis/mapper*。

```java
public interface EmpMapper {
	List<Emp> selectAll();
}
```

*EmpMapper.xml*：

```xml
<mapper namespace="org.codeArt.mapper.EmpMapper">
    <select id="selectAll" resultType="org.codeArt.pojo.Emp">
        select * from `emp`
    </select>
</mapper>
```

*namespace* 为 mapper 层接口的全路径。映射文件需要和接口的名称保持一样。SQL 语句的 id 和 接口下的方法名称需要保持一样，不然会抛出 SQL 绑定异常。在编译之后，映射文件需要和 mapper 层接口在同一目录，这也就是为什么上面要求 *resources* 文件夹下和接口使用一样的路径。

在 *sqlMapConfig.xml* 配置文件中加载对应的映射文件：

```xml
<mapper>
    <mapper class="org.codeArt.mapper.EmpMapper"/>
</mapper>
```

编写单元测试：

```java
@Test
public void testProxySelectAll() {
    EmpMapper mapper = session.getMapper(EmpMapper.class);
    List<Emp> list = mapper.selectAll();
    list.forEach(System.out::println);
}	
```

这条语句的底层使用了动态代理模式，动态创建一个 EmpMapper 的一个代理对象并赋给接口引用。所以在MyBatis 中不需要显式提供 Mapper 接口的实现类，这也是简单的地方。