# Spring(1)-IoC

# 一. 简介

## 1.1 Spring概念

Spring 框架是由于软件开发的复杂性而创建的。Spring 使用的是基本的 JavaBean 来完成以前只可能由 **EJB** 完成的事情。然而，Spring 的用途不仅仅限于服务端开发。从简单性、可测试性和解耦合性角度而言，绝大部分的 Java 应用都可以从 Spring 中收益。

- 目的：解决企业应用开发的复杂性。
- 功能：使用基本的 JavaBean 代替 EJB，并提供了更多的企业应用功能。
- 范围：任何 Java 应用。

简单来讲，Spring 是一个轻量级的控制反转(IoC)和面向切面编程(AOP)的框架。

官网：https://spring.io

**Spring 的优势**：

1. **方便解耦，简化开发**：

Spring 通过容器，将对象的创建从代码中剥离出来，交给 Spring 控制，避免直接编码造成模块之间的耦合度高,用户也不必自己编码处理对象的单例和多例控制，主要关注接口功能即可，不用关注具体使用哪个实现类和实现细节问题。

2. **AOP 切面编程**：

AOP 切面编程是程序设计的一种概念，Spring 对该概念实现的比较好，通过切面编程我们可以在不修改原有代码的情况下实现功能的增加，通常用于事务控制、日志记录、性能检测、权限控制等。

3. **声明式事务**： 	

事务的控制可以托管给 Spring，我们通过注解或者配置文件声明事务的处理方式即可，不用我们自己去编码处理。

4. **整合 Junit，方便测试**： 	

Spring 整合 Junit 单元测试，对于项目的功能都可以进行轻松快速的测试，便于我们调试程序。

5. **方便整合各种优秀的框架**：

 SSM：Spring + SpringMVC + MyBatis   		

 SSH：Spring + Hibernate + Struts

6. **丰富的功能封装**：

Spring 对 JavaEE(JDBC，JAVAMail)都进行了一系列的封装，简化我们对于 API 的使用，提高程序的开发效率。

7. **规范的源码学习样本**：

Spring 的源码设计巧妙，结构清晰，大量使用了设计模式，是 Java 代码规范编写的典范，也是高级程序员面试中经常会问到的源码。

**Spring 体系图**：

![spring overview](asset/image/spring-overview.png)

### 1.1.1 Data Access/Integration

数据访问/集成层包括 JDBC、ORM、OXM、JMS 和 Transactions 模块，具体介绍如下。 		

- JDBC 模块：提供了一个 JDBC 的抽象层，大幅度减少了在开发过程中对数据库操作的编码。 			
- ORM 模块：对流行的对象关系映射 API，包括 JPA、JDO、Hibernate 和 iBatis 提供了的集成层。 			
- OXM 模块：提供了一个支持对象/XML 映射的抽象层实现，如 JAXB、Castor、XMLBeans、JiBX 和 XStream。 			
- JMS 模块：指 Java 消息服务，包含的功能为生产和消费的信息。 			
- Transactions 事务模块：支持编程和声明式事务管理实现特殊接口类，并为所有的 POJO。 			

### 1.1.2 Web 模块

Spring 的 Web 层包括 Web、[Servlet](http://c.biancheng.net/servlet/)、Struts 和 Portlet 组件，具体介绍如下。 		

- Web 模块：提供了基本的 Web 开发集成特性，例如多文件上传功能、使用的 Servlet 监听器的 IoC 容器初始化以及 Web 应用上下文。 			
- Servlet 模块：包括 Spring 模型—视图—控制器（MVC）实现 Web 应用程序。 			
- Struts 模块：包含支持类内的 Spring 应用程序，集成了经典的 Struts Web 层。 			
- Portlet 模块：提供了在 Portlet 环境中使用 MVC实现，类似 Web-Servlet 模块的功能。 			

### 1.1.3 Core Container

Spring 的核心容器是其他模块建立的基础，由 Beans 模块、Core 核心模块、Context 上下文模块和 Expression Language 表达式语言模块组成，具体介绍如下。 		

- Beans 模块：提供了 BeanFactory，是工厂模式的经典实现，Spring 将管理对象称为 Bean。 			
- Core 核心模块：提供了 Spring 框架的基本组成部分，包括 IoC 和 DI 功能。 			
- Context 上下文模块：建立在核心和 Beans 模块的基础之上，它是访问定义和配置任何对象的媒介。ApplicationContext 接口是上下文模块的焦点。 			
- Expression Language 模块：是运行时查询和操作对象图的强大的表达式语言。 			

### 1.1.4 其他模块

Spring的其他模块还有 AOP、Aspects、Instrumentation 以及 Test 模块，具体介绍如下。

- AOP 模块：提供了面向切面编程实现，允许定义方法拦截器和切入点，将代码按照功能进行分离，以降低耦合性。 			
- Aspects 模块：提供与 AspectJ 的集成，是一个功能强大且成熟的面向切面编程（AOP）框架。 			
- Instrumentation 模块：提供了类工具的支持和类加载器的实现，可以在特定的应用服务器中使用。 			
- Test 模块：支持 Spring 组件，使用 JUnit 或 TestNG 框架的测试。

## 1.2 IoC概念

控制反转(Inversion of Control，缩写为 IoC)，是面向对象编程中的一种设计原则，可以用来降低计算机代码之间的耦合度。其中最常见的方式叫做**依赖注入(Dependency Injection，简称 DI)**，还有一种方式叫**依赖查找(Dependency Lookup)**。通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体将其所依赖的对象引用传递给它。也可以说，依赖注入到对象中，

简单的说就是，创建对象的权利，或者是控制的位置，由 JAVA 代码转移到 Spring 容器，由 Spring 的容器控制对象的创建，就是控制反转，Spring 创建对象时，会读取配置文件中的信息，然后使用反射给我们创建好对象之后在容器中存储起来，当我们需要某个对象时，通过 id 获取对象即可，不需要我们自己去 `new`。

总之，创建对象的过程交给容器。

## 1.3 IoC原理

1. 首先使用 Spring 框架之前，需要准备一个 xml 配置文件，配置所有需要加载的 Bean 对象，然后通过 xml 解析技术读取配置文件，比如说 *dom4j*。

声明 Bean 对象：

```xml
<bean id="user" class="org.mybatis.pojo.User"></bean>
```

2. 使用反射技术实例化对象，放入到一个 map 集合中，也就是 IoC 容器。

```java
Class clazz = Class.forName("org.mybatis.pojo.User");
// 实例化对象，前提是该类有无参构造方法
Object obj = clazz.newInstance();
// 将对象放入到singletionRegistry(单例注册中心)中
map.put("user", obj);
```

3. 工厂模式调用 *getBean* 方法返回 Bean 对象。

```java
public Object getBean(String beanName) {
    return map.get(beanName);
}
```

**IoC 顶层接口**：`BeanFactory`，IoC 容器基本功能接口，是 Spring 内部使用的接口，我们在处理业务时一般不直接使用该接口。

**ApplicationContext**：`BeanFactory` 的子接口，提供更多更强大的功能，研发人员一般使用的接口。

# 二. 依赖注入

首先需要引入 Spring 依赖包：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.12.RELEASE</version>
</dependency>
```

在 *resourcers* 文件夹下创建 *application-context.xml* 配置文件，需要在 New 菜单中选择 *Spring Config*，引入 Spring 之后，IDEA 会自动显示这个选项。

声明一个 User 类：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class User {
    private Integer id;
    private String username;
}
```

在 *application-context.xml* 中编写 Bean：

```xml
<bean id="user" class="org.codeArt.pojo.User"/>
```

编写单元测试：

```java
public class TestApplicationContext {
    private ApplicationContext context;

    @Before
    public void before() {
        context = new ClassPathXmlApplicationContext("application-context.xml");
    }
    
    @Test
    public void testApplicationContext() {
        User user = context.getBean("user", User.class);
        System.out.println(user);
    }
}
```

## 2.1 XML方式实现DI

使用 `bean` 标签可以声明一个 Bean 对象，`bean` 标签常见属性如下：

```xml
<bean id="user" class="org.codeArt.pojo.User" name="user1" scope="prototype" 
      lazy-init="true"/>
```

- *id*：对象的 id，容器可以根据 id 获取 Bean 对象。
- *class*：类的全路径名。
- *scope*：控制对象单例多例和使用范围。

*scope* 属性有以下值：

| 作用域         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| singleton      | **默认值**，Spring IoC容器中只会存在一个共享的bean实例       |
| prototype      | 部署的bean，每一次获取都会产生一个新的bean实例，相当与一个new的操作 |
| request        | 表示该针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP request内有效 |
| session        | 表示该针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP session内有效 |
| global session | 类似于标准的HTTP Session作用域，不过它仅仅在基于portlet的web应用中才有意义 |

- *lazy-init*：懒加载，调用 *getBean* 方法的时候再去实例化对象。

### 2.1.1 set方法属性赋值

需要配置的类必须要有对应的 *setter* 方法才行，不然注入报错：

```xml
<bean id="user" class="org.codeArt.pojo.User">
    <property name="id" value="2"/>
    <property name="username" value="洛必达"/>
</bean>
```

`property` 标签是对象的属性。

### 2.1.2 有参构造属性赋值

使用 `constructor-arg` 标签给 Bean 对象的构造器注入值：

```xml
<bean id="user" class="org.codeArt.pojo.User">
    <constructor-arg name="id" value="2"/>
    <constructor-arg name="username" value="洛必达"/>
</bean>
```

还可以使用 `index` 属性指代参数的索引号进行注入：

```xml
<bean id="user4" class="org.codeArt.pojo.User">
    <constructor-arg index="0" value="2"/>
    <constructor-arg index="1" value="洛必达"/>
</bean>
```

### 2.1.3 通过p、c命名空间赋值

引入 *p* 和 *c* 命名空间进行注入：

```xml
<!-- p指代属性注入 -->
<bean id="user" class="org.codeArt.pojo.User" p:username="高斯" p:id="100"/>
<!-- c指代构造器注入 -->
<bean id="user" class="org.codeArt.pojo.User" c:id="123" c:username="伯努利"/>
```

前提是需要引入命名空间：

```
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```

### 2.1.4 注入空值和特殊符号

注入空值使用 `null` 标签，特殊符号是用 `&` 转移或者直接使用 `value` 标签传入 `<![CDATA[]]>` 转义即可：

```xml
<bean id="user7" class="org.codeArt.pojo.User" lazy-init="true">
    <property name="userid">
        <null/>
    </property>
    <property name="username" value="&amp;曹操"/>
    <property name="password">
        <value><![CDATA[&<123456>]]></value>
    </property>
</bean>
```

### 2.1.5 bean的引用

假如 Bean 对象有引用数据类型属性，那么可以使用 *ref* 属性配置引用：

```xml
<bean id="date" class="java.util.Date"/>
<bean id="user" class="org.codeArt.pojo.User">
    <property name="id" value="2"/>
    <property name="username" value="洛必达"/>
    <property name="birthday" ref="date"/>
</bean>
```

还可以使用内部 Bean 嵌套：

```xml
<bean id="user" class="org.codeArt.pojo.User">
    <property name="id" value="2"/>
    <property name="username" value="洛必达"/>
    <property name="birthday">
        <bean class="org.codeArt.pojo.User">
            <property name="id" value="3"/>
            <property name="username" value="高斯"/>
        </bean>
    </property>
</bean>
```

嵌套的原理是使用反射技术获取 *get* 方法，然后获得对象之后再赋值。

### 2.1.6 注入集合

声明 Book 类：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class Book {
    private String isbn;
    private String author;
}
```

声明 Student 类：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class Student {
    private String[] books;
    private Set<String> bookSet;
    private List<String> bookList;
    private Map<String, String> bookMap;
    private List<Book> bookList2;
}
```

可以使用 *util* 命名空间在 xml 配置文件配置集合，引入 *util* 命名空间：

```
xmlns:util="http://www.springframework.org/schema/util"
```

配置集合：

```xml
<util:list id="outerBookPojoList">
    <bean id="book1" class="org.codeArt.pojo.Book" p:isbn="123456" p:author="洛必达"/>
    <bean id="book2" class="org.codeArt.pojo.Book" p:isbn="654321" p:author="高斯"/>
    <bean id="book3" class="org.codeArt.pojo.Book" p:isbn="809501" p:author="欧拉"/>
</util:list>
```

给集合属性注入值：

```xml
<bean class="org.codeArt.pojo.Student" id="student" lazy-init="true">
    <property name="books">
        <array>
            <value>JAVA</value>
            <value>MySQL</value>
            <value>Spring</value>
        </array>
    </property>
    <property name="bookSet">
        <set>
            <value>Mybatis</value>
            <value>HTML</value>
            <value>CSS</value>
        </set>
    </property>
    <property name="bookList">
        <list>
            <value>Mybatis</value>
            <value>HTML</value>
            <value>CSS</value>
        </list>
    </property>
    <property name="bookMap">
        <map>
            <entry key="JAVA" value="高斯林"/>
            <entry key="Golang" value="肯汤普森"/>
            <entry key="hotspot" value="几米"/>
        </map>
    </property>
    <property name="bookList2" ref="outerBookPojoList"/>
</bean>
```

`map` 标签的子标签 `entry` 属性或值假如是其他引用数据类型的话可以使用 *key-ref* 或者 *value-ref* 属性，不然默认就是 `String`。还可以使用 *value-type* 指定 value 类型。

总结：*List* 属性使用 `list` 标签，值使用 `value` 标签。*Set* 属性使用 `set` 标签，值使用 `value` 标签。*Map* 属性使用 `map` 标签，值使用 `entry` 标签。

*util* 命名空间还可以声明 Map 或者 Set 集合：

```xml
<util:map>
    <entry key="1" value="洛必达"/>
    <entry key="2" value="曹操"/>
    <entry key="3" value="刘备"/>
</util:map>

<!-- 可以使用type指定类型 -->
<util:set>
    <value type="java.lang.Integer">1</value>
    <value>2</value>
    <value>3</value>
</util:set>
```

### 2.1.7 工厂方式获取Bean

使用工厂方式获取 Bean 对象，特点就是 `bean` 标签中定义的 *class* 类不是返回的对象的类，而是生产该对象的工厂。Spring 框架中给我们定义好了一个工厂接口，可以生产对象的接口，我们可以通过工厂来获取 bean。

声明 `BookFactory` 类实现 `FactoryBean<Book>` 接口：

```java
public class BookFactory implements FactoryBean<Book> {

    // 这个方法用于返回bean
    @Override
    public Book getObject() {
        Book book = new Book();
        book.setIsbn("456131").setAuthor("Doug Lee");
        return book;
    }

    // 这个方法用于返回bean的类型
    @Override
    public Class<Book> getObjectType() {
        return Book.class;
    }
}
```

`FactoryBean` 的实例也称为**工厂 Bean**。

配置 xml 文件：

```xml
<bean id="book" class="org.codeArt.factory.BookFactory"/>
```

编写单元测试：

```java
@Test
public void testBookFactory() {
    Book book = context.getBean("book", Book.class);
    System.out.println(book);
}
```

*getBean* 方法调用时，传入的类型还是 `Book` 类，而不是 `BookFactory`，这一点需要注意。

## 2.2 Bean生命周期

### 2.2.1 生命周期

Bean 从创建到销毁经历的各个阶段以及每个阶段所调用的方法：

1. 通过构造方法创建 Bean 实例。
2. 调用 *setter* 方法为 Bean 的属性赋值。
3. 调用 Bean 的初始化方法，初始化方法需要在配置文件中指定。
4. 调用 *getBean* 方法获取 Bean 对象，然后调用 Bean 对象的方法。
5. 容器调用 *close* 方法销毁 Bean 对象，并调用 Bean 对象的销毁方法，销毁方法需要在配置文件中指定。

```java
public class User {

    private Integer id;

    private String username;

    private String password;
    
    public User() {
        System.out.println("第一阶段：调用无参构造");
    }

    public User(Integer userid, String username, String password) {
        this.id = userid;
        this.username = username;
        this.password = password;
    }

    public void setUserid(Integer userid) {
        this.id = userid;
    }

    public void setUsername(String username) {
        System.out.println("第二阶段：给属性赋值");
        this.username = username;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public void init() {
        System.out.println("第三阶段：调用初始化方法");
    }

    @Override
    public String toString() {
        System.out.println("第四阶段：获取Bean");
        return "User{" + "id=" + id + ", username='" + username + '\'' + ", 
            password='" + password + '\'' + '}';
    }

    public void destroy() {
        System.out.println("第五阶段：调用销毁方法");
    }
}
```

*application-context.xml*：

```xml
<bean id="user" class="org.codeArt.pojo.User" init-method="init" destroy-method="destroy">
    <property name="username" value="洛必达"/>
</bean>
```

编写单元测试：

```java
@Test
public void testBeanLifeCycle() {
    // 需要强转为ConfigurableApplicationContext才可以调用close方法
    // close方法是定义在ApplicationContext子接口里面的
    ConfigurableApplicationContext context0 = (ConfigurableApplicationContext) context;
    User user = context0.getBean("user", User.class);
    System.out.println(user);
    context0.close();
}
```

### 2.2.2 BeanPostProcessor

Bean 的后置处理器 BeanPostProcessor，**可以在调用初始化方法前后做一些后置操作**，也就是说可以人为干预 Bean 的初始化的前后过程。

创建后置处理器类，实现 `BeanPostProcessor` 接口，重写两个默认方法，根据需求而定，也可以只重写一个：

```java
public class IBeanPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof User) {
            System.out.println("BeanPostProcessor: User 初始化方法之前");
        }
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof User) {
            System.out.println("BeanPostProcessor: User 初始化方法之后");
        }
        return bean;
    }
}
```

最后 Bean 对象一定要返回，不然就无法获取了。

在配置文件中声明，*id* 属性不要也中：

```xml
<bean id="userPostProcessor" class="org.codeArt.processor.IBeanPostProcessor"/>
```

**BeanPostProcessor接口作用**：

如果我们想在 Spring 容器中完成 Bean 实例化、配置以及其他初始化方法前后要添加一些自己逻辑处理。我们需要定义一个或多个 `BeanPostProcessor` 接口实现类，然后注册到 Spring IoC 容器中。

1. 接口中的两个方法都要将传入的 Bean 返回，而不能返回 null，如果返回的是 null，那么我们通过*getBean* 方法将得不到目标。

2. `ApplicationContext` 会自动检测在配置文件中实现了 `BeanPostProcessor` 接口的所有 Bean，并把它们注册为后置处理器，然后在容器创建 Bean 的适当时候调用它，因此部署一个后置处理器同部署其他的 Bean 并没有什么区别。而使用 `BeanFactory` 实现的时候，Bean 后置处理器必须通过代码显式地去注册，在 IoC 容器继承体系中的 `ConfigurableBeanFactory` 接口中定义了注册方法。

### 2.2.3 BeanFactoryPostProcessor



## 2.3 Bean自动装配

通过 `property` 标签可以手动指定给属性进行注入，也可以通过自动装配，完成属性的自动注入，简化 DI 配置。

声明 `A` 类和 `B` 类：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class A {

    private B b;
}
public class B {

}
```

*application-context.xml*：

```xml
<bean id="b" class="org.codeArt.pojo.B"/>
<bean id="a" class="org.codeArt.pojo.A" autowire="byName"/>
```

使用 *autowire* 属性可以进行自动装配：

- *byName*：根据名称自动装配，自动找配置文件中 id 和 类属性名称一样的 Bean 对象然后进行注入，上例中 `A` 类的属性 *b* 和 配置文件中 *id* 为 `b` 的一样，所以可以自动注入。
- *byType*：根据类型自动装配，自动找配置文件中类型和属性类型一样的，但是这样可能会发生歧义，Spring 无法判断到底装配哪个 Bean 对象，所以尽量保证只有一个这样的 Bean，不然会报错。

编写单元测试：

```java
@Test
public void testAutowire() {
    A a = context.getBean("a", A.class);
    System.out.println(a);  // A(b=org.codeArt.pojo.B@a4102b8)
}
```

## 2.4 使用外部属性配置文件

Spring 容器可以读取外部 properties 属性配置文件，可以将文件中的信息注入给 Bean 对象。例如：引入 Druid 数据源依赖，配置连接池信息。

1. 导入依赖：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.6</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.25</version>
</dependency>
```

2. 在 *resources* 文件夹下创建 *jdbc.properties* 文件：

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/test?useSSL=false&useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
username=root
password=root
```

3. 引入外部文件：

声明 *context* 命名空间：

```
xmlns:context="http://www.springframework.org/schema/context"
```

引入 properties 文件：

```xml
<context:property-placeholder location="classpath:jdbc.properties"/>
```

4. 配置 DruidDataSource：

`${}` 中内容指代 *jdbc.properties* 配置文件中的键。

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="username" value="${username}"/>
    <property name="password" value="${password}"/>
    <property name="url" value="${url}"/>
    <property name="driverClassName" value="${driver}"/>
    <property name="initialSize" value="1"/>
</bean>
```

编写单元测试：

```java
@Test
public void testDataSource() throws SQLException {
    DruidDataSource dataSource = context.getBean("dataSource", DruidDataSource.class);
    System.out.println(dataSource);
    Connection connection = dataSource.getConnection();
}
```

## 2.5 注解方式管理Bean

Spring 内置了几个不同层面的 Bean 的注解：

- `@Component`：放在类上，用于标记，告诉 Spring 当前类需要由容器实例化 Bean 并放入容器中。

下面为 `@Component` 的子注解：

- `@Controller`：用于 **controller** 层的 Bean。
- `@Service`：用于 **service** 层的 Bean。
- `@Repository`：用于 **mapper** 层的 Bean。

在 *application-context.xml* 中开启注解扫描配置：

```xml
<context:component-scan base-package="org.codeArt"/>
```

*base-package* 属性后面放要扫描的包路径，如果有多个包需要扫描，可以使用逗号隔开：

```
org.mybatis,org.springframework,org.apache
```

可以使用注解的 *value* 属性指定 Bean 的 *id*：

```java
@Component("user")
public class User {}
```

### 2.5.1 组件扫描注解过滤器

可以给 `context:component-scan` 传入子标签过滤**只扫描**的组件或者**只不扫描**的组件：

`context:include-filter`：扫描并加载只符合条件的组件。

`context:exclude-filter`：排除符合条件的组件，也就是不加载这些符合条件的组件。

使用过滤器的前提是设置 *use-default-filters* 属性为 `false`。

有两个属性：

- *type*：
  - **assignable**：指定扫描某个接口派生出来的类。
  - **annotation**：指定扫描使用某个注解的类。
  - **aspectj**：指定扫描 AspectJ 表达式相匹配的类。
  - **custom**：指定扫描自定义的实现了 *org.springframework.core.type.filter.TypeFilter* 接口的类 regex 指定扫描符合正则表达式的类。

- *expression*：根据 *type* 的不同，这个表达式的配置方式也不同。

e.g.：

```xml
<!-- 只加载@Controller注解 -->
<context:component-scan base-package="org.codeArt" use-default-filters="false">
    <context:include-filter type="annotation" 
                            expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

```xml
<!-- 排除@Controller注解 -->
<context:component-scan base-package="org.codeArt" use-default-filters="false">
    <context:exclude-filter type="annotation" 
                            expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

### 2.5.2 注解式DI

在 Spring 框架内，还可以使用注解来管理 Bean。Spring 提供了以下几个注解用来注入 Bean：

- `@Autowired`：根据属性数据类型自动装配，相当于 `bean` 标签的 *autowire* 属性的 *byType* 值。这意味着 IoC 容器中不能有两个类型一样的 Bean 对象。

`@Autowired` 注解会根据类型到容器中去寻找对应的对象，找到后给当前属性赋值，不需要依赖 *setter* 方法。属性类型可以是借口，会自动匹配对应的实现类对象。

- `@Qualifier`：根据属性名称注入依赖。

`@Qualifier` 注解配合 `@Autowired` 注解使用可以根据名称指定注入对象。

```java
// Spring 会自动到IoC容器找到名称为userMapper的Bean
@Autowired
@Qualifier("userMapper")
private UserMapper userMapper;
```

但是这种方式很少用，不如直接使用 `@Resource` 注解。

- `@Resource`：JDK 提供的注解。可以根据类型，也可以根据名称注入。

```java
@Resource(name = "userMapper")
private UserMapper userMapper;
```

- `@Value`：注入普通数据类型，八种基本数据类型和 `String`。还可以注入外部 *properties* 文件的数据。

```java
@Value("male")
private String gender;

@Value("10")
private Integer age;

// ${}可以引用properties文件的数据
@Value("${username}")
private String username;
```

### 2.5.3 配置类

Spring 支持使用配置类来配置整个项目，需要使用 `@ComponentScan` 注解。

```java
@PropertySource("classpath:words.properties")
@ComponentScan(basePackages = "org.codeArt")
public class BeanConfig {
    @Bean
    public User user() {
        return new User();
    }
}
```

`@ComponentScan` 注解的 *basePackages* 属性指定扫描的包，`@PropertySource` 注解的值指定引入的 *properties* 文件。

`@Bean` 注解可以修饰一个方法，返回值为需要放入 IoC 容器的 Bean 对象，修饰符需要 `public`。默认以方法的名称作为 Bean 对象的 id。但是也可以在 `@Bean` 注解中传值，手动指定 Bean 对象的 id。

```java
@Bean("user0")
public User user() {
    return new User();
}
```

编写单元测试：

```java
public class TestAnnotation {

    private ApplicationContext context;

    @Before
    public void before() {
        context = new AnnotationConfigApplicationContext(BeanConfig.class);
    }

    @Test
    public void testComponent() {
        User user = (User) context.getBean("user");
        System.out.println(user);
    }
}
```
