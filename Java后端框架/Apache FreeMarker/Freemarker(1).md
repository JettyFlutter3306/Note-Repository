# Freemarker(1)

# 一. 简介

Freemarker 一款用 Java 语言编写免费开源的模板引擎，它支持与 JSP 的 EL 表达式类似的指令。

# 二. 整合Servlet

通常 Freemarker 我们都整合 Spring Boot，这边整合 Servlet 只做了解。

导入依赖：

```xml
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>3.0.1</version>
</dependency>

<dependency>
  <groupId>org.freemarker</groupId>
  <artifactId>freemarker</artifactId>
  <version>2.3.23</version>
</dependency>
```

编写 *web.xml* 配置文件：

```xml
<servlet>
    <servlet-name>freemarkerServlet</servlet-name>
    <servlet-class>freemarker.ext.servlet.FreemarkerServlet</servlet-class>
    <init-param>
        <param-name>TemplatePath</param-name>
        <param-value>/</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>freemarkerServlet</servlet-name>
    <url-pattern>*.ftlh</url-pattern>
</servlet-mapping>
```

接下来和 JSP 一样使用即可。

# 三. 常用指令

## 3.1 遍历List

编写 *Controller*：

```java
@Controller
public class HelloController {
    @Autowired
    private UserService userService;
    
    @GetMapping("show")
    public ModelAndView show() {
        ModelAndView mv = new ModelAndView();
        List<User> list = userService.selectAll();
        mv.addObject("list", list);
        mv.setViewName("show");
        return mv;
    }
}
```

页面代码：

```html
<table>
  <tr>
    <th>id</th>
    <th>username</th>
    <th>age</th>
  </tr>
  <#list list as user>
    <tr>
      <td>${user.id}</td>
      <td>${user.username}</td>
      <td>${user.age}</td>
    </tr>
  </#list>    
</table>
```

## 3.2 遍历Map

编写 *Controller*：

```java
@Controller
public class HelloController {
    @Autowired
    private UserService userService;
    
    @GetMapping("show")
    public ModelAndView show() {
        ModelAndView mv = new ModelAndView();
        List<User> list = userService.selectAll();
        Map<String, User> map = list.stream().collect(Collectors.groupingBy(User::getId));
        mv.addObject("map", map);
        mv.setViewName("show");
        return mv;
    }
}
```

页面代码：

输出固定 id 号的用户信息：

```html
id: ${map['100'].id}
username: ${map['100'].username}
age: ${map['100'].age}
```

遍历 Map：

```html
<table>
  <tr>
    <th>index</th>
    <th>id</th>
    <th>username</th>
    <th>age</th>
  </tr>
  <#list map?keys as k>
    <tr>
      <td>${k_index}</td>
      <td>${k}</td>
      <td>${map[k].username}</td>
      <td>${map[k].age}</td>
    </tr>
  </#list>
</table>
```

FreeMarker 在遍历 Map 集合是，Key 必须是 `String`。

## 3.3 if指令

if 指令即判断指令，是常用的 FTL 指令，Freemarker 在解析时遇到 if 会进行判断，条件为真则输出 if 中间的内容，否则跳过内容不再输出。

if 中支持的运算符：

- 算数运算符 Freemarker 表达式中完全支持算术运算，Freemarker 支持的算术运算符包括：`+`、`-`、`*`、`/`、`%`。

- 逻辑运算符有如下几个:

  - 逻辑与：`&&`。
  - 逻辑非：`||`。
  - 逻辑非：`!`。

  逻辑运算符只能作用于布尔值，否则将产生错误。

- 比较运算符有如下几个：

  - `=` 或者 `==`：判断两个值是否相等。
  - `!=`：判断两个值是否不等。
  - `>`：或者 `gt`：判断左边值是否大于右边值。
  - `>=` 或者 `gte`：判断左边值是否大于等于右边值。
  - `<` 或者 `lt`：判断左边值是否小于右边值。
  - `<=` 或者 `lte`：判断左边值是否小于等于右边值。

`=` 和 `!=` 可以用于字符串，数值和日期来比较是否相等，但 `=` 和 `!=` 两边必须是相同类型的值，否则会产生错误，而且 FreeMarker 是精确比较，"x"，"x "，"X" 是不等的。其它的运行符可以作用于数字和日期，但不能作用于字符串。大部分的时候，使用 `gt` 等字母运算符代替 `>` 会有更好的效果，因为 FreeMarker 会把 `>` 解释成 FTL 标签的结束字符。当然，也可以使用括号来避免这种情况，如：`<#if (x>y)>`。

如何判断空值：

1. 判断某变量是否存在使用 `??` 用法为：`variable??`，如果该变量存在，返回 true，否则返回 false。例：为防止 stus 为空报错可以加上判断。

2. 缺失变量默认值使用 `!` 使用 `!` 要以指定一个默认值，当变量为空时显示默认值。例： `${name!''}` 表示如果 `name` 为空显示空字符串。如果是嵌套对象则建议使用 `()` 括起来。

```html
<#if empList??>
    <#list empList as emp>
        <tr <#if emp_index%2 ==0 > style="background-color: gray" </#if>>
            <td>${emp_index}</td>
            <td>${emp.empno}</td>
            <td <#if emp.ename == 'KING'> style="color: aqua" </#if>>${emp.ename}</td>
            <td>${emp.job}</td>
            <td>${emp.mgr!'无'}</td>
            <td <#if emp.sal gte 2000.0> style="color: red" </#if>>${emp.sal}</td>
            <td>${emp.comm!'0'}</td>
            <td>${emp.deptno}</td>
		 </tr>
	</#list>
</#if>
```

## 3.4 内置函数

内建函数语法格式： 

```
variable + ? + function()
```

- 内建函数获取某个集合的大小：

```html
${collection?size}
```

- 内建函数日期格式化：

```html
显示年月日:          ${today?date}
显示时分秒：         ${today?time}
显示日期+时间：       ${today?datetime}
自定义格式化：        ${today?string("yyyy-MM")}
```

- 内建函数将 JSON 字符串转成对象：

```html
<#assign text="{'bank':'工商银行','account':'10101920201920212'}"/>
<#assign data=text?eval/>
开户行：${data.bank} 账号：${data.account}
```

`assign` 的作用是定义一个变量。