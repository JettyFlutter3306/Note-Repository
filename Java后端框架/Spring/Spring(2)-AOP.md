# Spring(2)-AOP

# 一. 概念

## 1.1 AOP简介

在软件行业，AOP 为 **Aspect Oriented Programming** 的缩写，意为：**面向切面编程**，通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术。AOP 是 OOP 的延续，是软件开发中的一个热点，也是 Spring 框架中的一个重要内容，是函数式编程的一种衍生范式。利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

AOP 切面编程一般可以帮助我们在不修改现有代码的情况下，对程序的功能进行拓展，往往用于实现**日志处理，权限控制，性能监测，事务控制**等。

AOP 实现的原理就是动态代理，Spring AOP 在有接口的情况下，使用 JDK 动态代理，在没有接口的情况下使用 Cglib 动态代理。

## 1.2 AOP术语

- **连接点(Join Point)**：

类里面那些**可以被增强的方法**被称为连接点。表示在程序中明确定义的点，典型的包括方法调用，对类成员的访问以及异常处理程序块的执行等，它自身还可以嵌套其他 Join Point。

- **切入点(Pointcut)**：

**实际被增强的方法**被称为切入点。表示一组 Join Point，这些 Join Point 或是通过逻辑关系组合起来，或者通过通配、正则表达式等方式集中起来，它定义了相应的 Advice 将要发生的地方。

- **通知(Advice)**：

**实际增强的逻辑部分称为通知(增加的功能)**。Advice 定义了在 Pointcut 里面定义的程序点具体要做的操作，它通过 *before*、*after* 和 *around* 来区别是在每个 Join point 之前、之后还是代替执行的代码。 

通知类型：

1. **前置通知(Before Advice)**：调用切点方法前执行的逻辑。
2. **后置通知(After Advice)**：调用切点方法最后执行的逻辑。
3. **环绕通知(Around Advice)**：调用切点方法前后都要执行的逻辑，在前置通知之前和在后置通知之后。
4. **异常通知(After Throwing Advice)**：调用切点方法抛出异常执行的逻辑，正常返回之后不会执行。
5. **返回通知(After Returning Advice)**：调用切点方法正常返回执行的逻辑，抛出异常之后不会执行。

- **目标对象(Target)**：

**被增强功能的对象(被代理的对象)**，织入 Advice 的目标对象。

- **切面(Aspect)**：

表现为功能相关的一些 Advice 方法放在一起声明称的一个 Java 类。Aspect 声明类似于 Java 中的类声明，在 Aspect 中会包含着一些 Pointcut 以及相应的 Advice。

-  **织入(Weaving)**：

创建代理对象并实现功能增强的声明并运行的过程。将 Aspect 和其他连接对象连接起来，并创建 Adviced Object 的过程。

# 二. AOP注解方式实现

AspectJ 本身并不是 Spring 框架中的组成部分，它是一个独立的 AOP 框架，一般把 AspectJ 和 Spring 框架的AOP 依赖一起使用，所以要导入一个独立的依赖。

实现的方式通常有两种：

1. 基于注解方式实现(**必会**)。
2. 基于 XML 配置方式(**了解**)。

## 2.1 准备工作

之前已经导入了 *spring-context* 依赖，现在只需要导入以下依赖：

```xml
<!-- 间接引入了aspectjweaver，无需重复引入 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.2.12.RELEASE</version>
</dependency>

<dependency>
    <groupId>aopalliance</groupId>
    <artifactId>aopalliance</artifactId>
    <version>1.0</version>
</dependency>
```

*aopalliance* 包提供了 AOP 的注解和一些拦截器接口，这些注解不是 Spring 提供的，所以要额外引入。但是，这些接口和注解不是由用户使用而是给 Spring 框架使用的。

## 2.2 切点表达式

Spring 框架借助了 AspectJ 框架来解析切点表达式。**切点表达式就是通过一个表达式来确定 AOP 要增强的是哪个或者哪些方法。**

语法结构：

```
execution([PermissionModifier][ReturnedValueType][FullClassName][MethodName](Variables))
```

e.g.：

指定切点为 `UserMapperImpl` 的 *add* 方法：

```
execution(* org.codeArt.mapper.UserMapperImpl.add(..))
```

指定切点为 `UserMapperImpl` 的所有方法：

```
execution(* org.codeArt.mapper.UserMapperImpl.*(..))
```

指定切点为 *mapper* 包下所有的类中的所有方法：

```
execution(* org.codeArt.mapper.*.*(..))
```

指定切点为 *mapper* 包下所有的类中的 *add* 方法：

```
execution(* org.codeArt.mapper.*.add(..))
```

指定切点为 *mapper* 包下所有的类中的 *add* 开头的方法：

```
execution(* org.codeArt.mapper.*.add*(..))
```

## 2.3 实操

开启注解扫描和 AOP 切面编程自动生成代理对象配置。开启之后 Spring 框架会自动识别添加了 `@Aspect` 注解的组件，不然 AOP 是不生效的。

```xml
<context:component-scan base-package="org.codeArt"/>
<aop:aspectj-autoproxy/>
```

声明接口 `UserMapper`：

```java
public interface UserMapper {
    int add(User user);
}
```

声明实现类 `UserMapperImpl`：

```java
@Repository
public class UserMapperImpl implements UserMapper {
    @Override
    public int add(User user) {
        // ...
    }
}
```

声明切面 `MapperAspect`：

```java
@Order(1)
@Aspect
@Component
public class MapperAspect {
    
    // 定义公共切点，可以直接切接口的方法，同样生效
    @Pointcut("execution(* org.codeArt.mapper.UserMapper.add(..))")
    public void pointcut() {
        
    }

    // 引入公共切点，值为公共切点方法名
    // 前置通知: 切点方法执行之前先执行的功能
    // 参数列表可以用JoinPoint接收切点对象
    @Before("pointcut()")
    public void before(JoinPoint point) {
        System.out.println("Method Before Invoked");
//        Object[] args = point.getArgs();
//        System.out.println(Arrays.toString(args));
    }

    /**
     * 后置通知：方法执行之后要增强的功能
     * 无论切点方法是否出现异常都会执行的方法
     */
    @After("pointcut()")
    public void after() {
        System.out.println("Method After Invoked");
    }

    /**
     * 返回通知：切点方法正常运行结束后增强的功能
     * 如果方法运行过程中出现异常，则该功能不运行
     * 需要用returning指定返回值名称，值和方法第2个参数名称一样
     */
    @AfterReturning(value = "pointcut()", returning = "result")
    public void afterReturning(JoinPoint point, Object result) {
        System.out.println("Method After Returning, return " + result);
    }

    /**
     * 异常通知：切点方法出现异常时运行的增强功能
     * 果方法运行没有出现异常，则该功能不运行
     * 需要通过throwing指定异常名称，值和方法参数名称相同
     */
    @AfterThrowing(value = "pointcut()", throwing = "e")
    public void afterThrowing(Exception e) {
        System.out.println("Method After Throwing");
    }

    /**
     * 环绕通知：在切点方法之前和之后都进行功能的增强
     * 需要在通知中定义方法执行的位置，并在执行位置之前和之后自定义增强的功能
     * 方法列表可以通过ProceedingJoinPoint获取执行的切点
     * 通过proceedingJoinPoint.proceed方法控制切点方法的执行位置
     * 无论切点方法是否有返回值，都必须要返回，使用Object类型返回值
     */
    @Around(value = "pointcut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        System.out.println("Method Around Before Invoked");
        Object o = point.proceed();
        System.out.println("Method Around After Invoked");
        return o;
    }
}
```

`@Component` 注解表示这是一个 Spring 组件，`@Aspect` 能表示这是一个切面让 Spring 能识别。`@Order` 注解设置了这个组件的优先级，值越小优先级越高，反之则越小。默认值是 `Ordered.LOWEST_PRECEDENCE` 也就是 `Integer.MAX_VALUE`，也是优先级最小值。优先级最大值是 `Integer.MIN_VALUE`。

编写单元测试：

```java
@Test
public void testAspect() {
    UserMapper userMapper = context.getBean(UserMapper.class);
    UserMapper.add(new User());
}
```

**运行顺序：**

环绕通知前置通知 ⇨ 前置通知 ⇨ 返回通知/异常通知 ⇨ 后置通知 ⇨ 环绕通知后置通知

```
Method Around Before Invoked
Method Before Invoked
Method After Returning, return 1
Method After Invoked
Method Around After Invoked
```

## 2.4 连接点对象API

**JoinPoint 对象**：

`JoinPoint` 对象封装了 Spring AOP 中切方法的信息，在切面方法中添加 `JoinPoint` 参数，就可以获取到封装了该方法信息的 `JoinPoint` 对象。常用 API：

- `Signature getSignature()`：获取封装了署名信息的对象，在该对象中可以获取到目标方法名，所属类的 Class 等信息。
- `Object[] getArgs()`：获取传入目标方法的参数对象。
- `Object getTarget()`：获取被代理的对象。
- `Object getThis`：获取代理对象。

**ProceedingJoinPont 对象**：

`ProceedingJoinPoint` 对象是 `JoinPoint` 的子接口，该对象只用在 `@Around` 的切面方法中。扩展的 API：

- `Object proceed() throws Throwable`：执行目标方法。
- `Object proceed(Object[] args) throws Throwable`：传入的新的参数去执行目标方法。

# 三. XML方式实现AOP

Spring AOP 还可以使用 XML 配置文件的方式来声明切面。

在 *application-context.xml* 配置文件中添加两个 Bean 对象：

```xml
<bean id="userMapper" class="org.codeArt.mapper.impl.UserMapperImpl"/>
<bean id="mapperAspect" class="org.codeArt.aspect.MapperAspect"/>
```

在 *application-context.xml* 配置文件中配置切入点：

```xml
<aop:config>
    <aop:pointcut id="pointcut" expression="execution(* org.codeArt.mapper.UserMapper.add(..))"/>
    <aop:aspect ref="mapperAspect">
        <aop:before method="before" pointcut-ref="pointcut"/>
        <aop:after method="after" pointcut-ref="pointcut"/>
        <aop:after-returning method="afterReturning" pointcut-ref="pointcut" returning="result"/>
        <aop:after-throwing method="afterThrowing" pointcut-ref="pointcut" throwing="e"/>
        <aop:around method="around" pointcut-ref="pointcut"/>
    </aop:aspect>
</aop:config>
```

`aop:pointcut` 标签定义了公共切入点，*expression* 属性定义切点表达式。

`aop:aspect` 标签定义了切面，*ref* 属性引用切面类，下面几个子标签对应着不同的通知类型，*pointcut-ref* 属性传入公共切入点的 id。其他的属性比如返回值和抛出异常和上面的注解式切面都一样。
