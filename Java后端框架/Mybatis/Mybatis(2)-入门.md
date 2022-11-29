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









## 7.6 bind标签











## 7.7 sql标签









## 7.8 foreach标签

























# 八. 多表查询

## 8.1 关联查询







## 8.2 级联查询









# 九. 注解开发











# 十. 缓存

## 10.1 一级缓存









## 10.2 二级缓存







## 10.3 三方缓存







# 十一. 逆向工程











