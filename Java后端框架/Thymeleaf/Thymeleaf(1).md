# Thymeleaf(1)

# 一. 简介

## 1.1 什么是Thymeleaf

Thymeleaf 的主要目标是将优雅的自然模板带到开发工作流程中，并将 HTML 在浏览器中正确显示，并且可以作为静态原型，让开发团队能更容易地协作。Thymeleaf 能够处理 HTML，XML，JavaScript，CSS 甚至纯文本。 	

  长期以来，JSP 在视图领域有非常重要的地位，随着时间的变迁，出现了一位新的挑者：Thymeleaf。Thymeleaf 是原生的，不依赖于标签库。它能够在接受原始 HTML 的地方进行编辑和渲染。因为它没有与 Servlet 规范耦合，因此 Thymeleaf 模板能进入 JSP 所无法涉足的领域。

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





## 2.2 th:value







## 2.3 th:if







## 2.4 th:each







## 2.5 th:href





## 2.6 th:onclick





## 2.7 标准变量表达式运算符







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









## 3.3 numbers









## 3.4 域对象













