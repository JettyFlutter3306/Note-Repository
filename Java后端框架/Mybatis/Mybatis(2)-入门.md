# Mybatis(2)

# 六. 代理模式下应用

## 6.1 模糊查询

在进行模糊查询时，在映射文件中可以使用 **CONCAT** 函数来连接参数和通配符。另外注意对于特殊字符，比如 `<`，不能直接书写，应该使用字符实体替换。

```java
public interface EmpMapper {
    /**
     * 根据姓名模糊查询
     */
    List<Emp> selectByEname(String name);
}
```

*EmpMapper.xml*：

```xml
<select id="selectByName" resultType="org.codeArt.pojo.Emp">
    select
    	*
    from
    	`emp`
    where
    	name
    like
    	CONCAT('%', #{name}, '%')
</select>
```

## 6.2 主键自增回填

MySQL 支持主键自增。有时候完成添加后需要立刻获取刚刚自增的主键，由下一个操作来使用。比如结算购物车后，主订单的主键确定后，需要作为后续订单明细项的外键存在。如何拿到主键呢，MyBatis 提供了支持，可以非常简单的获取。

*DeptMapper.java*：

```java
public interface DeptMapper {
    int insertOne(Dept dept);
}
```

*DeptMapper.xml*：

```xml
<insert id="insertOne" useGeneratedKeys="true" keyProperty="id">
    insert into `dept`
    values (default, #{name}, #{location})
</insert>
<!-- or -->
<insert id="insertOne">
    <selectKey order="AFTER" keyProperty="id" resultType="int">
        select @@identity
    </selectKey>
    insert into `dept`
    values (default, #{name}, #{location})
</insert>
```

编写单元测试：

```java
@Test
public void testInsertOne() {
    Dept dept = new Dept();
    dept.setName("NetWork").setLocation("California");
    DeptMapper mapper = session.getMapper(DeptMapper.class);
    int i = mapper.insertOne(dept);
    System.out.println(i);
    System.out.println(dept);
    session.commit(true);
}
```

插入成功过后，之前创建 dept 变量的 id 属性被自动赋值为插入后的自增 ID，打印可以清晰看到。

两种实现方式原理：

**方式 1：**

*useGeneratedKeys*：表示要使用自增的主键。

*keyProperty*：表示把自增的主键赋给 JavaBean 的哪个成员变量。

以添加 Dept 对象为例，添加前 Dept 对象的 id 是空的，添加完毕后可以通过 *getId* 方法获取自增的主键。

**方式 2：**

*order*：取值 `AFTER|BEFORE`，表示在新增之后或之前执行 `<selectKey>` 中的 SQL 命令。

*keyProperty*：执行 `select @@identity` 后结果填充到哪个属性中。

*resultType*：结果类型。

在很多应用场景中需要新增数据后获取到新增数据的主键值，针对这样的需求一般由三种解决方式：主键自定义，用户通过 UUID 或时间戳等方式生成唯一主键，把这个值当做主键值。在分布式场景中应用较多。查询后通过`select max(PRIMARY KEY) from` 表获取主键最大值。这种方式在多线程访问情况下可能出现问题。查询后通过 `select @@identity` 获取最新生成主键。要求这条 SQL 必须在 *insert* 操作之后，且数据库连接没有关闭。

## 6.3 实现DML操作

实现 DML 操作和查询操作其实差不多，都是获取 Mapper 的代理对象，然后调用对应的方法。

# 七. 动态SQL

经常遇到很多按照很多查询条件进行查询的情况，比如京东根据不同的条件筛选商品。其中经常出现很多条件不取值的情况，在后台应该如何完成最终的 SQL 语句呢？

如果采用 JDBC 进行处理，需要根据条件是否取值进行 SQL 语句的拼接，一般情况下是使用 StringBuilder 类及其 *append* 方法实现，还是有些繁琐的。如果你有使用 JDBC 或其它类似框架的经验，你就能体会到根据不同条件拼接 SQL 语句的痛苦。例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL 这一特性可以彻底摆脱这种痛苦。 		

MyBatis 在简化操作方法提出了动态 SQL 功能，将使用 Java 代码拼接 SQL 语句，改变为在 XML 映射文件中截止标签拼接 SQL 语句。相比而言，大大减少了代码量，更灵活、高度可配置、利于后期维护。

MyBatis 中动态 SQL 是编写在 *mapper.xml* 中的，其语法和 JSTL 类似，但是却是基于强大的 OGNL 表达式实现的。

MyBatis 也可以在注解中配置 SQL，但是由于注解功能受限，尤其是对于复杂的 SQL 语句，可读性很差，所以较少使用。

## 7.1 if标签

`if` 标签用于条件判断，假如条件满足那就显示语句，否则隐藏。

*EmpMapper.java*：

```java
public interface EmpMapper {
    List<Emp> selectByCondition(Emp emp);
}
```

*EmpMapper.xml*：

```xml
<select id="selectByCondition" resultType="org.codeArt.pojo.Emp">
    select
    	*
    from
    	`emp`
    where 1 = 1
    <if test="id != null">
        and id = #{id}
    </if>
    <if test="name != null and name != ''">
        and name like CONCAT('%', #{name}, '%')
    </if>  
</select>
```

假如没有查询条件，那么 where 关键字就不会拼接上去，假如只有一个查询条件，那么**第一个** `and` 会被自动删除。

## 7.2 where标签

`where` 标签用于处理 `and` 的数量，使用了 `where` 标签之后就不需要使用 `1 = 1` 这个恒成立表达式了，Mybatis 会自动处理 `and` 问题，自动干掉第一个 `and`，假如条件多于两个的话。

```xml
<select id="selectByCondition" resultType="org.codeArt.pojo.Emp">
    select
    	*
    from
    	`emp`
    <where>
        <if test="id != null">
            and id = #{id}
        </if>
        <if test="name != null and name != ''">
            and name like CONCAT('%', #{name}, '%')
        </if>
    </where>
</select>
```

## 7.3 choose标签

`choose` 选择标签会判断 `when` 标签里面的条件，假如条件成立，那么其他的 `when` 标签内部的条件将不再判断。

```xml
<select id="selectByCondition" resultType="org.codeArt.pojo.Emp">
    select
    	*
    from
    	`emp`
    <where>
        <choose>
            <when test="id != null">and id = #{id}</when>
            <when test="name != null and name != ''">and name = #{name}</when>
            <when test="job != null and job != ''">and job = #{job}</when>
            <when test="hireDate != null">and hire_date = #{hireDate}</when>
        </choose>
    </where>
</select>
```

## 7.4 set标签

`set` 标签用于处理 **update** 操作的 `,` 问题，因为在拼接 `,` 时，总会碰到多一个 `,` 或者少一个 `,` 的问题。

*EmpMapper.java*：

```java
int updateByCondition(Emp emp);
```

*EmpMapper.xml*：

```xml
<update id="updateByCondition">
    update
    	`emp`
    <set>
        <if test="name != null and name != ''">
            , name = #{name}
        </if>
        <if test="job != null and job != ''">
            , job = #{job}
        </if>
        <if test="mgr != null and mgr != ''">
            , mgr = #{mgr}
        </if>
        <if test="hireDate != null">
            , hire_date = #{hireDate}
        </if>
        <if test="sal != null">
            , sal = #{sal}
        </if>
        <if test="comm != null">
            , comm = #{comm}
        </if>
        <if test="deptId != null">
            , deptId = #{deptId}
        </if>
    </set>
    where
    	id = #{id}
</update>
```

假如只更新一个字段，那么 Mybatis 会自动地干掉第一个 `,` 符号。

## 7.5 trim标签

`trim` 标签用于添加公共前后缀，可以用来优化 `set` 标签和 `where` 标签，有四个属性：

- *prefix*：需要增加的前缀。
- *prefixOverrides*：需要去除的前缀。
- *suffix*：需要增加的后缀。
- *suffixOverrides*：需要去除的后缀。

```xml
<update id="updateByCondition">
    update
    	`emp`
    <trim prefix="set" prefixOverrides=",">
        <if test="name != null and name != ''">
            , name = #{name}
        </if>
        <if test="job != null and job != ''">
            , job = #{job}
        </if>
        <if test="mgr != null and mgr != ''">
            , mgr = #{mgr}
        </if>
        <if test="hireDate != null">
            , hire_date = #{hireDate}
        </if>
        <if test="sal != null">
            , sal = #{sal}
        </if>
        <if test="comm != null">
            , comm = #{comm}
        </if>
        <if test="deptId != null">
            , deptId = #{deptId}
        </if>
    </trim>
    where
    	id = #{id}
</update>
```

```xml
<select id="selectByCondition" resultType="org.codeArt.pojo.Emp">
    select
    	*
    from
    	`emp`
    <trim prefix="where" prefixOverrides="and">
        <if test="id != null">
            and id = #{id}
        </if>
        <if test="name != null and name != ''">
            and name like CONCAT('%', #{name}, '%')
        </if>
    </trim>
</select>
```

其实 `where` 和 `set` 标签都是 `trim` 标签的特例。

## 7.6 bind标签

`bind` 标签一般用于处理模糊查询的模板。

```java
List<Emp> selectByName(String name);
```

```xml
<select id="selectByName" resultType="emp">
    <bind name="likePattern" value="'%' + param1 + '%'"></bind>
    select *
    from emp
    where name like #{likePattern}
</select>
```

## 7.7 sql标签

`sql` 标签一般用于抽取公共的查询字段，还可以配合 `include` 标签使用引入公共查询字段。

```xml
<sql id="baseColumn"><id, name, job, hire_date</sql>
<sql id="baseSelect">
    select <include refid="baseColmn"></include> from `emp`
</sql>
<select id="selectByCondition" resultType="org.codeArt.pojo.Emp">
    <include refid="baseSelect"></include>
    <where>
        <if test="id != null">
            and id = #{id}
        </if>
        <if test="name != null and name != ''">
            and name like CONCAT('%', #{name}, '%')
        </if>
    </where>
</select>
```

## 7.8 foreach标签

`foreach` 标签，用于遍历迭代外部传入的集合数据，生成重复的有规律的数据。

**标签属性：**

1. *collection*：遍历的集合或者是数组，若是数组，使用别名 **array**，假如是集合，那么则使用别名 **list**。
2. *separator*：多个元素取出的时候，需要使用的分隔符。
3. *open*：公共前缀，用于开头的字符或字符串。
4. *close*：公共后缀，用于结尾的字符或字符串。
5. *item*：迭代项，临时变量。

**根据 ids 查找员工列表**：

```java
List<Emp> selectByIds(List<Integer> ids);
```

```xml
<select id="selectByIds" resultType="org.codeArt.pojo.Emp">
    select * from `emp`
    where id in
    <foreach collection="list" separator="," open="(" close=")" item="id">
        #{id}
    </foreach>
</select>
```

最终生成的语句：

```sql
select * from `emp` where id in (1, 2, 3)
```

**根据员工集合批量插入**：

```java
int insertBatch(List<Emp> list);
```

```xml
<insert id="insertBatch">
    insert into `emp` values
    <foreach collection="list" separator="," item="emp">
        (default, #{emp.name}, #{emp.job}, #{emp.hireDate})
    </foreach>
</insert>
```

没有公共前缀和后缀所以不需要 *opne* 和 *close* 属性。

最终生成的语句：

```sql
insert into `emp` values
(default, '洛必达', '工程师', '2000-10-01'),
(default, '高斯', '工程师', '2000-10-01'),
(default, '欧拉', '工程师', '2000-10-01'),
```

# 八. 多表查询

## 8.1 关联查询

### 8.1.1 手动处理resultMap

在 mapper 映射文件中，声明 `resultMap` 标签，通常它的 *id* 属性设为 `BaseMapper`。

```xml
<resultMap type="org.codeArt.pojo.Emp" id="BaseMapper" autoMapping="true">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="job" property="job"/>
</resultMap>
```

```xml
<select id="selectById" resultMap="BaseMapper">
    select * from `emp` where id = #{id}
</select>
```

设置 *autoMapping* 为 `true` 后，就不需要一个个编写 `result`标签了，因为这样 Mybatis 会做一个忽略大小写自动映射的操作，再加上先前设置了驼峰命名自动映射为下划线命名，基本上不用怎么写 `result` 标签。也就是会在命名冲突的时候起别名会写 `result` 的内容。但是 `id` 标签一定要写，Mybatis 是根据这个进行映射的，不然会报错。

### 8.1.2 一对一关联查询

给 Emp 类添加一个 dept 字段，数据类型是 Dept 类。

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
    private Dept dept;
}
```

*EmpMapper.java*：

```java
public interface EmpMapper {
    Emp selectEmpJoinDeptById(int id);
}
```

现在要做一个与部门表 dept 的关联查询，那么就需要手动配置 `resultMap` 标签，使用 `association` 标签进行关联：

```xml
<resultMap type="org.codeArt.pojo.Emp" id="BaseMapper">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="job" property="job"/>
    <result column="sal" property="sal"/>
    <result column="hire_date" property="hireDate"/>
    <result column="mgr" property="mgr"/>
    <result column="comm" property="comm"/>
    <result column="deptId" property="deptId"/>
    <association property="dept" javaType="org.codeArt.pojo.Dept" 
                 resultMap="org.codeArt.mapper.DeptMapper.BaseMapper"/>
</resultMap>
```

假如 `resultMap` 在其他 XML 文件中定义过了，那么就可以引入直接使用，不需要二次重写 `result` 标签属性映射。

`result` 标签的 *column* 属性表示数据库表的列名，*property* 属性表示实体类的属性。

*EmpMapper.xml*：

```xml
<select id="selectEmpJoinDeptById" resultMap="BaseMapper">
    select
    	*
    from
    	`emp` e left join `dept` d on e.deptId = d.id
    where
    	e.id = #{id}
</select>
```

编写单元测试：

```java
@Test
public void testSelectEmpJoinDeptById() {
    EmpMapper mapper = session.getMapper(EmpMapper.class);
    Emp emp = mapper.selectEmpJoinDeptById(7499);
    System.out.println(emp);
}
```

多表查询后，多出来的字段自动映射到 dept 字段中。

### 8.1.3 一对多关联查询

现在要根据部门 id 查询对应的员工，那么就需要在 Dept 类中添加 employess 字段，数据类型是 `List<Emp>`。

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class Dept {
    private Integer id;
    private String name;
    private String location;
    private List<Emp> employees;
}
```

*DeptMapper.java*：

```java
public interface DeptMapper {
    Dept selectDeptJoinEmpById(int id);
}
```

在 Mapper 映射文件配置 `resultMap` 标签，需要使用一个 `collection` 标签，表示一个集合：

```xml
<resultMap type="org.codeArt.pojo.Dept" id="BaseMapper" autoMapping="true">
        <id column="id" property="id"/>
    	<collection property="employees" ofType="org.codeArt.pojo.Emp">
            <id column="uid" property="id"/>
    	</collection>
</resultMap>
```

同理 `resultMap` 写过一遍，就不需要重复写，只需要从别的命名空间引入即可。**前提是两个类之间没有超过两个以上的耦合字段，只能是一方到另一方，不可逆，不然就会产生映射错误，这样的话推荐使用中间表或者起别名搞定。**

`resultMap` 实在不想写的话，那么就开启驼峰和下划线自动映射选项。

```xml
<select id="selectDeptJoinEmpById" resultMap="baseMapper">
    select
    	d.*,
        e.id as uid,
        e.job,
        e.sal,
        e.hire_date,
        e.mgr,
        e.comm,
        e.deptId
    from
    	`dept` d left join `emp` e on d.id = e.deptId
    where
    	d.id = #{id}
</select>
```

编写单元测试：

```java
@Test
public void testSelectDeptJoinEmpById() {
    DeptMapper mapper = session.getMapper(DeptMapper.class);
    Dept dept = mapper.selectDeptJoinEmpById(20);
    dept.getEmployees().forEach(System.out::println);
}
```

### 8.1.4 多对多关联查询

根据项目编号查询项目信息，以及参与到该项目之中的所有的员工信息。

实体类：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class Project {
    private Integer pid;
    private String pName;
    private Integer money;
    private List<ProjectRecord> projectRecords;
}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class ProjectRecord {
    private Integer empNo;
    private Integer pid;
    private Emp emp;
}
```

*ProjectMapper.java*：

```java
public interface ProjectMapper {
    Project selectProjectJoinEmpByPid(int pid);
}
```

*ProjectMapper.xml*：

```xml
<mapper namespace="org.codeArt.mapper.ProjectMapper">
    <resultMap type="org.codeArt.pojo.Project" id="baseMapper" autoMapping="true">
        <id column="pid" property="pid"/>
        <collection property="projectRecords" ofType="org.codeArt.pojo.ProjectRecord">
            <id column="empno" property="empNo"/>
            <association property="emp" javaType="org.codeArt.pojo.Emp" 
                         resultMap="org.codeArt.mapper.EmpMapper.baseMapper"/>
        </collection>
    </resultMap>
    
    <select id="selectProjectJoinEmpByPid" resultMap="baseMapper">
        select 
            * 
        from
            `project` p
            left join `project_record` pr on p.pid = pr.pid
            left join `emp` e on e.id = pr.empNo
        where
            p.pid = #{pid}
    </select>
</mapper>
```

编写单元测试：

```java
@Test
public void testSelectProjectJoinEmpByPid() {
    ProjectMapper mapper = session.getMapper(ProjectMapper.class);
    Project project = mapper.selectProjectJoinEmpByPid(2);
    System.out.println(project.getPid());
    System.out.println(project.getPName());
    System.out.println(project.getMoney());
    List<ProjectRecord> list = project.getProjectRecords();
    list.forEach(System.out::println);
}
```

## 8.2 级联查询

级联查询，顾名思义，就是利于数据库表间的外键关联关系进行自动的级联查询操作。使用 MyBatis 实现级联查询，除了实体类增加关联属性外，还需要在映射文件中进行配置。

### 8.2.1 立即加载

```xml
<resultMap type="org.codeArt.pojo.Dept" id="baseMapper" autoMapping="true">
    <id column="id" property="id"/>
    <collection property="employees"
                select="org.codeArt.mapper.EmpMapper.selectAllByDeptId"
                javaType="list"
                column="id"
                jdbcType="INTEGER"
                fetchType="eager"/>
</resultMap>
```

**属性**：

- *javaType*：实体类的属性数据类型。
- *column*：给另一个 SQL 语句传入的参数列。
- *jdbcType*：参数对应 JDBC 的数据类型。
- *fetchType*：加载方式，`eager` 积极加载，`lazy` 懒加载。

### 8.2.2 延迟加载

**延迟加载，又称按需加载。**延迟加载的内容等到真正使用时才去进行加载（查询）。多用在关联对象或集合中。 		

延迟加载的好处：先从单表查询、需要时再从关联表去关联查询，大大降低数据库在单位时间内的查询工作量,将工作在时间上的分配更加均匀，而且单表要比关联查询多张表速度要快。 		

延迟加载的设置：

1. 全局开关，在 *sqlMapConfig.xml* 中打开延迟加载的开关。配置完成后所有的 `association` 和 `collection` 元素都生效。

```xml
<settings>
    <setting name="lazyLoadingEnabled" value="true"/>
    <setting name="aggressiveLazyLoading" value="true"/>
</settings>
```

**lazyLoadingEnabled**：是否开启延迟加载。是 Mybatis 是否启用懒加载的全局开关。当开启时，所有关联对象都会延迟加载。特定关联关系中可通过设置 *fetchType* 属性来覆盖该项的开关状态。

**aggressiveLazyLoading**：当开启时，任何方法的调用都会懒加载对象的所有属性。否则，每个属性会按需加载。

2. 分开关，指定的 *association* 和 *collection* 元素中配置 *fetchType* 属性。eager：表示立刻加载；lazy：表示延迟加载。**将覆盖全局延迟设置**。

## 8.3 总结

**resultMap 常见属性**：

| 属性        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| property    | 需要映射到JavaBean的属性名称                                 |
| javaType    | property的类型，一个完整的类名，或者是一个类型别名。如果你匹配的是一个JavaBean，那MyBatis 通常会自行检测到 |
| column      | 数据表的列名或者列别名                                       |
| jdbcType    | column 在数据库表中的类型。这个属性只在insert，update或delete的时候针对允许空的列有用。JDBC需要这项，但MyBatis不需要 |
| fetchType   | 自动延迟加载                                                 |
| select      | association、collection的属性，使用哪个查询查询属性的值，要求指定namespace+id的全名称 |
| ofType      | collection的属性，指明集合中元素的类型（即泛型类型）         |
| autoMapping | 允许自动忽略大小写自动映射字段                               |

# 九. 注解开发

可以使用 Mybatis 内置的注解简化 CRUD 的开发：

```java
public interface DeptMapper {

    @Select("select * from dept where deptno = #{deptno}")
    Dept findByDeptno(int deptno);
    
    @Update("update dept set dname =#{dname}, loc = #{loc} where deptno = #{deptno}")
    int updateDept(Dept dept);
    
    @Insert("insert into dept values(DEFAULT, #{dname}, #{loc})")
    int addDept(Dept dept);
    
    @Delete("delete from dept where deptno = #{deptno}")
    int removeDept(int deptno);
}
```

# 十. 缓存

## 10.1 一级缓存

一级存储是 SqlSession 上的缓存，默认开启，是一种内存型缓存，不要求实体类对象实现 Serializable 接口。 

缓存中的数据使用键值对形式存储数据。
$$
namespace + sqlId + args + offset >>> hash
$$
使用这个公式作为键，查询出的结果作为值。

**测试代码**：

```java
@Test
public void testSelectByIds() {
    EmpMapper mapper = session.getMapper(EmpMapper.class);
    List<Integer> list = new ArrayList<Integer>() {{
        add(1);
        add(2);
        add(3);
    }};
    List<Emp> list1 = mapper.selectByIds(list);
    list1.forEach(System.out::println);
    // 若中间调用commit方法提交事务，那么就会清空缓存
    // session.commit();
    EmpMapper mapper1 = session.getMapper(EmpMapper.class);
    List<Emp> list2 = mapper1.selectByIds(list);
    System.out.println(mapper == mapper1);  // false
    System.out.println(list1 == list2);     // true
}
```

同一个 Mapper 接口的代理对象在内存中不是单例的，而且 Mybatis 的一级缓存是默认开启的。

## 10.2 二级缓存

二级缓存是以 namespace 为标记的缓存，可以是由一个 SqlSessionFactory 创建的 SqlSession 之间共享缓存数据。默认并不开启。下面的代码中创建了两个 SqlSession，执行相同的 SQL 语句，尝试让第二个 SqlSession 使用第一个 SqlSession 查询后缓存的数据。要求实体类必须实现序列化接口。

*EmpMapper.xml*：

```xml
<mapper namespace="org.codeArt.mapper.EmpMapper">
    <cache/>
    <select id="selectById" resultType="org.codeArt.pojo.Emp" useCache="true" flushCache="false">
        select *
        from `emp`
        where id = #{id}
    </select>
</mapper>
```

测试代码：

```java
@Test
public void testSelectById() {
    EmpMapper mapper1 = session.getMapper(EmpMapper.class);
    Emp emp1 = mapper1.selectById(7521);
    System.out.println(emp1);
    // 提交之后才会放入二级缓存
    session.commit();
    EmpMapper mapper2 = session1.getMapper(EmpMapper.class);
    Emp emp2 = mapper2.selectById(7521);
    System.out.println(emp2);
}
```

注意其中的 *commit*，执行该命令后才会将该 SqlSession 的查询结果从一级缓存中放入二级缓存，供其他SqlSession 使用。另外执行 SqlSession 的 *close* 方法也会将该 SqlSession 的查询结果从一级缓存中放入二级缓存。两种方式区别在当前 SqlSession 是否关闭了。

假如运行显示查询了两次数据库，说明二级缓存没有生效。检查以下配置：

1. 全局开关：在 *sqlMapConfig.xml* 文件中的 `settings` 标签配置开启二级缓存。

```xml
<settings>
    <setting name="cacheEnabled" value="true"/>
</settings>
```

cacheEnabled 的默认值就是 true，所以这步的设置可以省略。

2. 分开关：在要开启二级缓存的 Mapper 文件中开启缓存。

```xml
<mapper namespace="org.codeArt.mapper.EmpMapper">
    <cache/>
</mapper>
```

3. 二级缓存未必完全使用内存，有可能占用硬盘存储，缓存中存储的 JavaBean 对象必须实现序列化接口。假如没有实现 Serializable 接口那么就会抛出异常。

**总结**：

- MyBatis的二级缓存的缓存介质有多种多样，而并不一定是在内存中，所以需要对 JavaBean 对象实现序列化接口。

- 二级缓存是以 namespace 为单位的，不同 namespace 下的操作互不影响。
- 加入 `cache` 元素后，会对相应命名空间所有的 select 元素查询结果进行缓存，而其中的 insert、update、delete 在操作是会清空整个 namespace 的缓存，这就是为了保证 DML 操作之后的数据一致性。
- `cache` 有一些可选的属性 *type*，*eviction*，*flushInterval*，*size*，*readOnly*，*blocking*。

```xml
<cache type="" readOnly="" eviction="" flushInterval="" size="" blocking=""/>
```

属性解释：

- *type*：自定义缓存类，要求实现 *org.apache.ibatis.cache.Cache* 接口，默认值 null。

- *readOnly*：是否只读，默认值 false。

  - true：给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了很重要的性能优势。

  - false：会返回缓存对象的拷贝(通过序列化)。这会慢一些，但是安全。

- eviction：缓存策略，默认值 **LRU**。
  - *LRU*：最近最少使用，移除最长时间不被使用的对象。
  - *FIFO*：先进先出，按对象进入缓存的顺序来移除它们。
  - *SOFT*：软引用，基于垃圾回收器状态和软引用规则移除对象。
  - *WEAK*：弱引用，更积极地基于垃圾收集器状态和弱引用规则移除对象。

- *flushInterval*：刷新间隔，毫秒为单位。默认为null，也就是没有刷新间隔，只有执行 update、insert、delete 语句才会刷新。
- *size*：缓存对象个数，默认值 1024。
- *blocking*：是否使用阻塞性缓存 BlockingCache，默认值 false。
  - true：在查询缓存时锁住对应的 Key，如果缓存命中了则会释放对应的锁，否则会在查询数据库以后再释放锁，保证只有一个线程到数据库中查找指定 Key 对应的数据。
  - false：不使用阻塞性缓存，性能更好。
-  如果在加入 Cache 元素的前提下让个别 `select` 元素不使用缓存，可以使用 *useCache* 属性，设置为false。
  - *useCache*： 控制当前 sql 语句是否启用缓存。
  - *flushCache*： 控制当前 sql 执行一次后是否刷新缓存。

```xml
<select id="selectById" resultType="org.codeArt.pojo.Emp" useCache="true" 
        flushCache="false">
    select *
    from `emp`
    where id = #{id}
</select>
```

## 10.3 三方缓存

分布式缓存框架：我们系统为了提高系统并发和性能，一般对系统进行分布式部署（集群部署方式）不使用分布缓存， 缓存的数据在各个服务单独存储，不方便系统开发。所以要使用分布式缓存对缓存数据进行集中管理。ehcache，redis，memcache 缓存框架。

Ehcache：是一种广泛使用的开源 Java 分布式缓存。主要面向通用缓存，JavaEE 和轻量级容器。它具有内存和磁盘存储功能。被用于大型复杂分布式 WebApplication 的。这里的三方缓存是作为二级缓存使用的。

```xml
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.0.2</version>
</dependency>

<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>2.10.1</version>
</dependency>
```

去各自的 Mapper 映射文件里，开启二级缓存，并把缓存类型指定为 EhcacheCache：

```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```

在资源目录下放置一个缓存配置文件，文件名为：*ehcache.xml* 内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="ehcache.xsd"
         updateCheck="true" monitoring="autodetect"
         dynamicConfig="true">
    <diskStore path="/Users/element/ehcache" />
    <defaultCache
            maxElementsInMemory="1000"
            maxElementsOnDisk="10000000"
            eternal="false"
            overflowToDisk="true"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
    </defaultCache>
</ehcache>
```

**Cache 配置：**

- *name*：Cache的唯一标识。
- *maxElementsInMemory*：内存中最大缓存对象数。
- *maxElementsOnDisk*：磁盘中最大缓存对象数，若是 0 表示无穷大。
- *eternal*：Element 是否永久有效，一但设置了，timeout 将不起作用。
- *overflowToDisk*：配置此属性，当内存中 Element 数量达到 *maxElementsInMemory* 时，Ehcache 将会Element 写到磁盘中。
- *timeToIdleSeconds*：设置 Element 在失效前的允许闲置时间。仅当 Element 不是永久有效时使用，可选属性，默认值是 0，也就是可闲置时间无穷大。
- *timeToLiveSeconds*：设置 Element 在失效前允许存活时间。最大时间介于创建时间和失效时间之间。仅当 Element 不是永久有效时使用，默认是 0，也就是 Element 存活时间无穷。
- *diskExpiryThreadIntervalSeconds*：磁盘失效线程运行时间间隔，默认是 120 秒。
- *diskSpoolBufferSizeMB*：这个参数设置 DiskStore(磁盘缓存)的缓存区大小。默认是 30 MB。每个 Cache 都应该有自己的一个缓冲区。
- *memoryStoreEvictionPolicy*：当达到 *maxElementsInMemory* 限制时，Ehcache 将会根据指定的策略去清理内存。默认策略是 LRU(最近最少使用)。你可以设置为 FIFO(先进先出)或是 LFU(较少使用)。

# 十一. 逆向工程

MyBatis 的一个主要的特点就是需要程序员自己编写 SQL，那么如果表太多的话，难免会很麻烦，所以 MyBatis官方提供了一个逆向工程，可以针对单表自动生成 MyBatis 执行所需要的代码（包括 mapper.xml，mapper.java，pojo）。一般在开发中，常用的逆向工程方式是通过数据库的表生成代码。

引入依赖：

```xml
<dependencies>
    <!-- mysql驱动包 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.25</version>
    </dependency>

    <!-- 日志包，方便查看执行信息 -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.36</version>
    </dependency>

    <!-- 代码生成工具jar -->
    <dependency>
        <groupId>org.mybatis.generator</groupId>
        <artifactId>mybatis-generator-core</artifactId>
        <version>1.3.2</version>
    </dependency>
</dependencies>
```

配置逆向工程配置文件，在 *resources* 目录下放置一个名为 *generatorConfig.xml* 的配置文件，文件内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
    PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
    "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="testTables" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        
        <jdbcConnection 
            driverClass="com.mysql.cj.jdbc.Driver"
            connectionURL="jdbc:mysql://127.0.0.1:3306/mydb?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai&amp;allowPublicKeyRetrieval=true"
            userId="root"
            password="root">
        </jdbcConnection>
        
        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和 
           NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        
        <!-- targetProject:生成PO类的位置 -->
        <javaModelGenerator targetPackage="org.codeArt.pojo"
                            targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        
        <!-- targetProject:mapper映射文件生成的位置 -->
        <sqlMapGenerator targetPackage="org.codeArt.mapper"
                         targetProject="src/main/resources">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        
        <!-- targetPackage：mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="org.codeArt.mapper"
                             targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>
        <!-- 指定数据库表 -->
        <table tableName="dept" domainObjectName="Dept"
               enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false">
            <columnOverride column="id" javaType="Integer"/>
        </table>
    </context>
</generatorConfiguration>
```

在 *resources* 目录下放置一个名为 *log4j.properties* 的配置文件，文件内容如下：

```properties
log4j.rootLogger=debug,stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.err
log4j.appender.stdout.layout=org.apache.log4j.SimpleLayout
log4j.appender.logfile=org.apache.log4j.FileAppender
log4j.appender.logfile.File=~/log
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %l %F %p %m%n
```

编写测试代码：

```java
public class GeneratorSqlmap {

    public static void generator() throws Exception {
        List<String> warnings = new ArrayList<>();
        boolean overwrite = true;
        File configFile = new File("src/test/resources/generatorConfig.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
        myBatisGenerator.generate(null);
    }

    public static void main(String[] args) throws Exception {
        try {
            generator();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

