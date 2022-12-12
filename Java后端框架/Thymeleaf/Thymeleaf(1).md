# Thymeleaf(1)

# 一. 简介

## 1.1 什么是Thymeleaf

Thymeleaf 的主要目标是将优雅的自然模板带到开发工作流程中，并将 HTML 在浏览器中正确显示，并且可以作为静态原型，让开发团队能更容易地协作。Thymeleaf 能够处理 HTML，XML，JavaScript，CSS 甚至纯文本。 	

  长期以来，JSP 在视图领域有非常重要的地位，随着时间的变迁，出现了一位新的挑战者：Thymeleaf。Thymeleaf 是原生的，不依赖于标签库。它能够在接受原始 HTML 的地方进行编辑和渲染。因为它没有与 Servlet 规范耦合，因此 Thymeleaf 模板能进入 JSP 所无法涉足的领域。

  Thymeleaf 在 Spring Boot 项目中放入到 *resources/templates* 中。这个文件夹中的内容是无法通过浏览器 URL 直接访问的(和 *WEB-INF* 效果一样)，所有 Thymeleaf 页面必须先走控制器。

# 二. 基础语法

Thymeleaf 通过**标准变量表达式**完成数据的展示和处理。

- 标准变量表达式必须依赖标签，不能独立使用。
- 标准变量表达式一般在开始标签中，以 `th` 开头。
- 语法为： `<tag th:***="${key}"></tag>`。
- 表达式中可以通过 `${}` 取出域中的值并放入标签的指定位置。
- `${}` 在这里不能单独使用，必须在 `th:` 后面的双引号里使用。

为了有 IDEA 语法提示，修改 HTML 页面中的 `<html>` 标签为：

```html
<html xmlns:th="http://www.thymeleaf.org">
```

## 2.1 th:text

`th:text` 属性作用是向 HTML 标签内部输出信息。

编写 Controller：

```java
@RequestMapping("show")
public String show(Map<String, Object> map) {
    map.put("msg", "test msg");
    return "index";
}
```

```html
<!-- 向span标签中输出字面值 -->
<span th:text="demo text"></span><br>
<!-- 从域对象中取出参数名称对应的值输出到标签中 -->
<span th:text="${msg}"></span><br>
```

## 2.2 th:value

`th:value` 用于操作表单元素的 *value* 属性。

```html
<!--向input标签中的value属性赋值-->
<input type="text" th:value="pageMessage"/>
<!--从域中根据参数名取出参数值 向input标签中的value属性赋值-->
<input type="text" th:value="${msg}"/>
```

## 2.3 th:if

`th:if` 用于判断是否满足条件，满足则显式元素否则不显示。

```html
<span th:if="${name}!='张三'">会显示</span>
```

## 2.4 th:each

`th:each` 用于循环迭代遍历。语法如下：

```
th:each="u, i : ${key}"
```

*i* 表示迭代状态对象，属性如下：

- *index*：当前迭代器的索引，从 0 开始。
- *count*：当前迭代对象的计数，从 1 开始。
- *size*：被迭代对象的长度。
- *even/odd*：布尔值，当前循环是否是偶数/奇数，从 0 开始。
- *first*：布尔值，当前循环的是否是第一条，如果是返回 true，否则返回 false。
- *last*：布尔值，当前循环的是否是最后一条，如果是则返回 true，否则返回 false。

编写 Controller：

```java
@Controller
public class ThymeleafController {
    @Autowired
    private EmpService empService;
    @RequestMapping("showEmp")
    public String showEmp(Map<String, Object> map) {
        List<Emp> empList = empService.findAll();
        map.put("empList", empList);
        map.put("emp", empList.get(0));
        return "showEmp";
    }
}
```

```html
展示单个员工信息:
<span th:if="${emp} != null">
  工号:<span th:text="${emp.empno}"></span><br/>
  姓名:<span th:text="${emp.ename}"></span><br/>
  职务:<span th:text="${emp.job}"></span><br/>
  上级:<span th:text="${emp.mgr}"></span><br/>
  入职日期:<span th:text="${emp.hiredate}"></span><br/>
  工资:<span th:text="${emp.sal}"></span><br/>
  补助:<span th:text="${emp.comm}"></span><br/>
  部门号:<span th:text="${emp.deptno}"></span><br/>
</span>
<hr/>
<span th:if="${empList} != null">
  <span  th:if="${empList.size()} ne 0">
    工号:<span th:text="${empList[0].empno}"></span><br/>
    姓名:<span th:text="${empList[0].ename}"></span><br/>
    职务:<span th:text="${empList[0].job}"></span><br/>
    上级:<span th:text="${empList[0].mgr}"></span><br/>
    入职日期:<span th:text="${empList[0].hiredate}"></span><br/>
    工资:<span th:text="${empList[0].sal}"></span><br/>
    补助:<span th:text="${empList[0].comm}"></span><br/>
    部门号:<span th:text="${empList[0].deptno}"></span><br/>
  </span>
</span>
<table  id="empTable" cellpadding="0px" cellspacing="0px">
  <tr>
    <th>索引</th>
    <th>序号</th>
    <th>总人数</th>
    <th>偶数索引?</th>
    <th>奇数索引?</th>
    <th>第一?</th>
    <th>最后?</th>
    <th>工号</th>
    <th>姓名</th>
    <th>职务</th>
    <th>上级</th>
    <th>入职日期</th>
    <th>工资</th>
    <th>补助</th>
    <th>部门号</th>
  </tr>
  <tr th:each="emp,i : ${empList}">
    <td th:text="${i.index}"></td>
    <td th:text="${i.count}"></td>
    <td th:text="${i.size}"></td>
    <td th:text="${i.odd}"></td>
    <td th:text="${i.even}"></td>
    <td th:text="${i.first}"></td>
    <td th:text="${i.last}"></td>
    <td th:text="${emp.empno}"></td>
    <td th:text="${emp.ename}"></td>
    <td th:text="${emp.job}"></td>
    <td th:text="${emp.mgr}"></td>
    <td th:text="${emp.hiredate}"></td>
    <td th:text="${emp.sal}"></td>
    <td th:text="${emp.comm}"></td>
    <td th:text="${emp.deptno}"></td>
  </tr>
</table>
```

## 2.5 th:href

`th:href` 属性用于设置 HTML 元素的 *href* 属性，取值时使用 `@{}` 取值。

```html
<a th:href="@{/getParam(id = 1, name = 'rock')}" >跳转</a>
<!-- 获取作用域值 -->
<a th:href="@{/getParam(name = ${stu.name}, age = ${stu.age})}">跳转二</a>
```

在 `()` 中可以给后端传参。

## 2.6 th:onclick

`th:onclick` 用于给元素绑定事件，单击事件并传递参数。

- 方式 1：仅仅支持数字和布尔类型参数的传递，字符串不支持。

```html
<a href="javascript:viod(0)"  th:onclick="'del('+${emp.empno}+')'">删除</a>
```

- 方式 2：支持数字和文本类型的参数传递。

```html
<a href="javascript:void(0)" th:onclick="delEmp([[${emp.empno}]], [[${emp.ename}]])">删除</a>
```

## 2.7 标准变量表达式运算符

- 算数运算符：`+`、`-`、`*`、`/`、`%`。

```html
<span th:text="1 + 1"></span>  <!-- 2 -->
<span th:text="'1' + 1"></span>  <!-- 2 -->
<span th:text="${emp.empno} + 1"></span>
<span th:text="${emp.empno + 1}"></span>
```

- 关系运算符：

```
gt:     great than（大于）>
ge：    great equal（大于等于）>=
eq：    equal（等于）==
lt：    less than（小于）<
le：    less equal（小于等于）<=
ne：    not equal（不等于）!= 
```

- 逻辑运算符：`&&`、`and`、`||`、`or`。

```html
<div th:text="1 > 0 and 2 < 3"></div>
<div th:text="1 > 0 and 2 > 3"></div>
<div th:text="1 > 0 or 2 < 3"></div>
<div th:text="1 > 0 or 2 > 3"></div>
<hr/>
<div th:text="${emp.sal ge 800}"></div>
<div th:text="${emp.sal } ge 800"></div>
<div th:text="${emp.sal ge 800} and ${emp.deptno eq 20}"></div>
<div th:text="(${emp.sal }ge 800) or (${emp.deptno } ne 20)"></div>
<div th:text="${emp.sal ge 800 or emp.deptno ne 20 }"></div>
```

在早期的 Thymeleaf 模板引擎框架中，逻辑运算符要写在 `${}` 的外边，目前我们 2.4.5 版本中,可以写在 `${}` 里面。

- 三目运算符：

```html
<tr th:each="emp, i : ${empList}" th:class="${i.odd} ? a : b">
```

- 处理空值：

```html
<td th:text="${emp.mgr} eq null ? 老板:${emp.mgr}"></td>
```

# 三. 内置对象

Thymeleaf 提供了一些内置对象，内置对象可直接在模板中使用。这些对象是以 `#` 引用的。

使用内置对象的语法时候，引用内置对象需要使用 `#`，大部分内置对象的名称都是以 `s` 结尾。

**常见的内置对象如下：**

- `#arrays`：数组操作的工具。
- `#aggregates`：操作数组或集合的工具。
- `#bools`：判断 boolean 类型的工具。
- `#calendars`：类似于 `#dates`，但是是 `java.util.Calendar` 类的方法。
- `#ctx`：上下文对象，可以从中获取所有的 Thymeleaf 内置对象。
- `#dates`：日期格式化内置对象，具体方法可以参照 `java.util.Date`。
- `#numbers`：数字格式化。
- `#strings`：字符串格式化，具体方法可以参照 `String`。
- `#objects`：参照 `java.lang.Object`。
- `#lists`：列表操作的工具，参照 `java.util.List`。
- `#sets`：Set 操作工具，参照 `java.util.Set`。
- `#maps`：Map 操作工具，参照 `java.util.Map`。
- `#messages`：操作消息的工具。

重点掌握：`#strings`、`#dates`、`#numbers` 和域对象。

## 3.1 strings

- `${#strings.isEmpyu(key)}`：判断字符串是否为空，如果为空返回 true，否则返回 false。
- `${#strings.contains(msg, target)}`：判断字符串是否包含指定的子串。
- `${#strings.startsWith(msg, prefix)}`：判断字符串是否包含指定前缀。
- `${#strings.endWith(msg, suffix)}`：判断字符串是否包含指定后缀。
- `${$strings.length(msg)}`：返回字符串长度。
- `${#strings.indexOf(msg, target)}`：查找子串的索引，如果没找到就返回 -1。

## 3.2 dates

- `${#dates.format(key)}`：格式化日期，默认的浏览器默认语言格式化标准。
- `${#dates.format(key, 'yyyy-MM-dd hh:mm:ss')}`：按照自定义的格式做日期格式转换。
- `${#dates.year(key)}`：取年。
- `${#dates.month(key)}`：取月。
- `${#dates.day(key)}`：取日。

## 3.3 numbers

- `${#dates.formatDecimal(number, 整数为, 整数位千分位标识符, 小数位, 小数位标识符)}`

```
// num = 999999.0
${#numbers.formatDecimal(num, 1, 'COMMA', 2, 'POINT')}  // 999,999.00
```

表示整数位至少一位，不足以 0 补齐。

## 3.4 域对象

- `HttpServletRequest`：

```html
request.setAttribute("msg", "hello");
<span th:text="${#httpServletRequest.getAttribute('msg')}"></span>
<span th:text="${#request.getAttribute('msg')}"></span>
```

- `HttpSession`：

```html
request.getSession().setAttribute("ses", "HttpSession");
<span th:text="${session.ses}"></span>
<span th:text="${#session.getAttribute('ses')}"></span>
```

- `ServletContext`：

```html
request.getServletContext().setAttribute("app", "Application");
<span th:text="${application.app}"></span>
<span th:text="${#servletContext.getAttribute('app')}"></span>
```
