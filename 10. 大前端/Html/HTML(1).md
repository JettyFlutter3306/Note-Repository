# HTML1

# 一. 简介

## 1.1 网络传输基石

三大基石：**URL，HTTP协议，HTML**。

URL：在 WWW 上，每一信息资源都有统一的且在网上唯一的地址，该地址就叫 URL（Uniform Resource Locator,统一资源定位符），它是 WWW 的统一资源定位标志，就是指网络地址。 		

HTTP：http 是一个简单的请求-响应协议，它通常运行在 TCP 之上。它指定了客户端可能发送给服务器什么样的消息以及得到什么样的响应。请求和响应消息的头以 ASCII 码形式给出；而消息内容则具有一个类似 **MIME** 的格式。这个简单模型是早期 Web 成功的有功之臣，因为它使得开发和部署是那么的直截了当。 		

HTML：HTML称为超文本标记语言。

## 1.2 什么是HTML

HTML 指的是超文本标记语言: **HyperText Markup Language**。

**超文本：** 	

      普通人 vs  超人（比普通的人厉害） 	

   普通文本  vs 超文本(比普通的文本厉害) 	

**标记：** 

标记 = 标签

标签：`<html> <body> <head>` 由尖括号包围起来的关键词 	

分类：双标记标签/封闭类型标签  `<p></p>`   单标记标签/非封闭类型标签  `<br/>` 	

语言：HTML是一个描述网页的语言

总结：学习HTML就是学习各种各样的标签，然后组成一个页面，这个页面可以被浏览器解析，解析完以后可以在浏览器中将页面进行展示。

## 1.3 HTML标准结构

先用普通文本文档新建一个文本，将文本的后缀改为.html  或者 .htm。

标准结构如下：

```html
<html>
    <head></head>
    <body>
    	this is my first html....
    </body>
</html>
```

# 二. HTML标签

## 2.1 基本结构

1. html 标签：

定义 HTML 文档，这个元素我们浏览器看到后就明白这是个HTML文档了，所以你的其它元素要包裹在它里面，标签限定了文档的开始点和结束点，在它们之间是文档的头部和主体。

2. head 标签：

head标签用于定义文档的头部，它是所有头部元素的容器。<head> 中的元素可以引用脚本、指示浏览器在哪里找到样式表。文档的头部描述了文档的各种属性和信息，包括文档的标题、在 Web 中的位置以及和其他文档的关系等。绝大多数文档头部包含的数据都不会真正作为内容显示给读者。 		

下面这些标签可用在 head 部分： 		

`<title>`、`<meta>`、`<link>`、`<style>`、 `<script>`、 `<base>`。 		

应该把 `<head>` 标签放在文档的开始处，紧跟在 `<html>` 后面，并处于 `<body>` 标签之前。 		

文档的头部经常会包含一些 `<meta>` 标签，用来告诉浏览器关于文档的附加信息。

3. body 标签：

body 元素是定义文档的主体。body 元素包含文档的所有内容（比如文本、超链接、图像、表格和列表等等。）body 是用在网页中的一种 HTML 标签，标签是用在网页中的一种 HTML 标签，表示网页的主体部分，也就是用户可以看到的内容，可以包含文本、图片、音频、视频等各种内容！

## 2.2 head

head 标签内部可以编写以下内容：

head标签中：放入页面的配置信息

head标签中可以加入：

`<title>`、`<meta>`、`<link>`、`<style>`、 `<script>`、` <base>`。

```html
<html>
    <head>
        <title>百度一下，你就知道</title>
        <meta charset="utf-8" />
        <meta name="author" content="flutter3306@gmail.com" />
        <meta name="keywords" content="第一个html页面" />
        <meta name="description" content="html css" />
        <link rel="shortcut icon" href="https://www.baidu.com/favicon.ico" 
              type="image/x-icon" />
    </head>
    <body>
        this is a html..你好
    </body>
</html>
```

### 2.2.1 title标签

title 标签用于设置页面的标题，也就是浏览器选项卡的名称。标签包裹的内容就是页面的标题：

```html
<title>百度一下，你就知道</title>
```

### 2.2.2 meta标签

meta 标签主要是用于对页面进行配置，它的 name 属性是比较重要的，content 属性的值表示对应的值，有如下值：

- author：页面作者
- keywords：页面的关键字，主要用于搜索分词
- description：页面的描述

**charset** 属性可以设置页面的编码格式，有两种书写方式，一种是简写，一种繁写：

```html
<!-- 简写 -->
<meta charset="utf-8">
<!-- 繁写 -->
<meta http-equiv="content-type" content="text/html;charset=utf-8">
```

**http-equiv** 属性还可以设置页面的刷新效果：

```html
<meta http-equiv="refresh" content="3;https://www.baidu.com">
```

### 2.2.3 link标签

link 标签可以引入外部文件，比如说 css：

```html
<link rel="stylesheet" type="text/css" href="./css/base.css">
```

还可以引入 icon：

```html
<link rel="shortcut icon" type="image/x-icon" href="https://www.baidu.com/favicon.ico">
```

### 2.2.4 base标签

base 标签可以设置当前页面 url 的根路径：

```html
<base href="http://localhost:8080/">
```

表示当前页面使用相对路径获取资源的前缀都是 href 属性的值。

比如：

```html
<img src="/img/pic1.png">
```

那么图片最终的路径就是：

```html
http://localhost:8080/img/pic1.png
```

## 2.3 body

### 2.3.1 文本标签

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>文本标签</title>
    </head>
    <body>
        <h1>h1</h1>
        <h2>h2</h2>
        <h3>h3</h3>
        <h4>h4</h4>
        <h5>h5</h5>
        <h6>h6</h6>
    </body>
</html>
```

**h1** 至 **h6** 标签主要用于设置标题，数字越小字号越大，而且只有 1 至 6 这 6 个标签。

**hr** 标签可以设置水平线：

```html
<!-- 宽度100像素，左对齐 -->
<hr width="100px" align="left">
```

align：默认 center，可选：[right | center]。

**p** 标签可以声明一段文字，段落文字自动换行，段落和段落之间有空行：

```html
<p>
    5月26日，“建议父母持合格父母证上岗”冲上微博热搜，迅速引发热议。在正在召开的全国两会上，
    全国政协委员许洪玲建议在社区举办家长课堂，建立“家长教育指导工作室”。
    针对准备入小学的家长开展相关课程教育，颁发“合格父母”上岗证随学生档案入学。
    5月26日，“建议父母持合格父母证上岗”冲上微博热搜，迅速引发热议。
    在正在召开的全国两会上，全国政协委员许洪玲建议在社区举办家长课堂，建立“家长教育指导工作室”。
    针对准备入小学的家长开展相关课程教育，颁发“合格父母”上岗证随学生档案入学。
</p>
```

**b** 标签可以加粗文字：

```html
<b>加粗</b>
```

**i** 标签可以倾斜文字：

```html
<i>倾斜</i?>
```

**u** 标签可以给文字下划线效果：

```html
<u>下划线</u>
```

**del** 标签可以给文字删除线效果：

```html
<del>删除线</del>
```

**pre** 预编译标签可以在页面上显示文字原效果：

```html
<pre>
	public static void main(String[] args) {
		System.out.println("Hello World");
	}	
</pre>
```

**br** 标签可以换行：

```html
<br>
```

**font** 标签可以设置字体，但是已经过时了，不建议使用，建议使用 css 设置 font-family 属性：

```html
<font color="red" size="16" face="consolas">你好世界</font>
```

### 2.3.2 多媒体标签

多媒体标签可以引入外部的视频，音频，图片等文件。

**img** 标签可以引入图片：

```html
<img src="img/ss6.jpg" width="300px" title="这是一个美女小姐姐" alt="图片加载失败">
```

**embed** 标签引入视频：

```html
<embed src="video/周杰伦 - 说好的幸福呢.mp4" width="500px" height="500px"
       allowFullScreen="true" quality="high" align="middle" 
       allowScriptAccess="always" type="application/x-shockwave-flash"></embed>
```

**embed** 标签引入音频：

```html
<embed src="music/青花瓷.mp3"></embed>
```

### 2.3.3 超链接标签

**超链接：**超链接简单来说就是，按内容链接，本质上是属于网页的一部分，它是一种允许我们同其他网页或站点之间进行连接的元素，各个网页链接在一起后，才能真正意义上构建一个网站。

**a** 标签可以跳转页面，标签包裹的内容就是链接的名称：

```html
<a href="http://www.baidu.com" target="_blank" alt="#">百度</a>
```

href 属性指的是要跳转的网页路径，可以相对路径，也可以是绝对的网络 url。

target 属性指的是如何打开，可选值：[_self | _blank]：

- _self：在当前页面打开
- _blank：在新页面打开

alt 属性用于设置找不到资源是显示的内容。

**a** 标签可以设置页面的锚点定位，只需要设置 href 属性的值多一个 `#` 符号即可：

```html
<a href="1F"></a>
<a href="2F"></a>
<a href="3F"></a>
<a href="4F"></a>
<a href="#1F">手机</a>
<a href="#2F">化妆品</a>
<a href="#3F">母婴产品</a>
<a href="#4F">书籍</a>
```

点击会跳转到对应的锚点，这个比较常用，比如考试页面经常会使用题号按钮跳转对应的题目。

### 2.3.4 列表标签

列表标签分为有序列表和无序列表，有序列表有序号，无需列表没有序号，取而代之的是黑点。

有序列表：

```html
<ol type="A" start="3">
    <li>JAVASE</li>
    <li>ORACLE</li>
    <li>MYSQL</li>
    <li>HTML</li>
    <li>CSS</li>
    <li>JS</li>
</ol>
```

type 指定序号的类型，默认是数字从 1 开始，A 表示是字母，默认从 A 开始，start 设置起始位置。

type 可选值：[1 | a | A | i | I]。**i** 和 **I** 表示罗马数字。

有序列表：

```html
<ul type="square">
    <li>睁眼</li>
    <li>穿衣服</li>
    <li>上厕所</li>
    <li>吃早饭</li>
    <li>洗漱</li>
    <li>出门</li>
</ul>
```

type 指定无需列表前面的黑点形状，默认圆形，这边设置为方形。

### 2.3.5 表格标签

表格标签是最常用的数据展示标签，用途十分广泛，声明一个表格：

```html
<table border="1px" cellspacing="0" bgcolor="grey">
    <tr bgcolor="bisque">
        <th>学号</th>
        <th>姓名</th>
        <th>年纪</th>
        <th>成绩</th>
    </tr>
    <tr>
        <td align="center">1001</td>
        <td>丽丽</td>
        <td>19</td>
        <td rowspan="3">90.5</td>
    </tr>
    <tr>
        <td colspan="2" align="center">2006</td>
        <td>30</td>
    </tr>
    <tr>
        <td>3007</td>
        <td>小明</td>
        <td>18</td>
    </tr>
</table>
```

**th** 标签表示表头，**tr** 标签表示行，**td** 标签表示单个单元格。一般在浏览器解析的时候，会在表头加上 `<thead>` 标签，在内容部分加上 `<tbody>` 标签。

table 可用属性：

- border：设置边框大小
- cellspacing：设置单元格和边框之间的空隙
- align：设置对齐方式，可选：[center | left | right]
- background：设置背景图片
- bgcolor：设置背景颜色

td 可用属性：

- rowspan：行合并
- colspan：列合并

# 三. iframe

## 3.1 内嵌框架

内嵌框架是用于在网页中嵌入一个网页并让它在网页中显示。添加内嵌框架的语法:

```html
<iframe src="url"></iframe>
```

url 指定独立的网页路径。

书籍展示页面：

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
        <iframe src="书籍导航页面.html" height="700px" width="30%"></iframe>
    </body>
</html>
```

## 3.2 框架集合

**frameset** 标签可定义一个框架集。它被用来组织多个窗口(框架)。每个框架存有独立的文档。在其最简单的应用中，**frameset** 标签仅仅会规定在框架集中存在多少列或多少行。必须使用 cols 或 rows 属性。

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <!--框架集合：和body是并列的概念，不要将框架集合放入body中-->
    <frameset rows="20%,*,30%">
        <frame />
        <frameset cols="30%,40%,*">
            <frame />
            <frame src="index.html"/>
            <frame />
        </frameset>
        <frameset cols="50%,*">
            <frame />
            <frame />
        </frameset>
    </frameset>
</html>
```

# 四. 表单

表单在 Web 网页中用来给访问者填写信息，从而能采集客户端信息，使网页具有交互的功能。一般是将表单设计在一个Html 文档中，当用户填写完信息后做提交(submit)操作，于是表单的内容就从客户端的浏览器传送到服务器上，经过服务器上程序处理后，再将用户所需信息传送回客户端的浏览器上，这样网页就具有了交互性。这里我们只讲怎样使用Html 标志来设计表单。 	

所有的用户输入内容的地方都用表单来写，如登录注册、搜索框。 	

一个表单一般应该包含用户填写信息的输入框,提交按钮等，这些输入框,按钮叫做控件,表单很像容器,它能够容纳各种各样的控件。

```html
<form action＝"url" method="post" name="myform" ></form>
```

## 4.1 表单元素

### 4.1.1 文本框

**input** 标签使用很广泛，通过type属性的不同值，来表现不同的形态。

`type="text"` 文本框，里面文字可见，表单元素必须有一个属性：name 有了name 才可以提交数据,才可以采集数据然后提交的时候会以键值对的形式拼到一起。value 就是文本框中的具体内容键值对：name=value的形式如果 value 提前写好，那么默认效果就是 value 中内容。一般默认提示语：用 **placeholder** 属性，不会用 value，value 只是文本框中的值。

```html
<input type="text" name="username" value="洛必达">
```

**readonly** 属性可以指定文本框只读，有三种写法：

```
readonly="readonly"
readonly="true"
readonly
```

凡是属性值为布尔类型的，都可以使用第三种方法，此种写法默认值就是 true。

```html
<input type="text" name="username" value="洛必达" readonly>
```

**disabled** 属性可以指定文本框禁用：

```html
<input type="text" name="username" value="洛必达" disabled>
```

**type** 属性设置为 **password** 可以隐藏密码：

```html
<input type="password" name="password" value="">
```

### 4.1.2 单选按钮

```html
<input type="radio" name="gender" value="1" checked>男
<input type="radio" name="gender" value="0">女
```

一组单选按钮，必须通过 name 属性来控制，让它们在一个分组中，然后在一个分组里只能选择一个。正常状态下，提交数据为：gender=on ，后台不能区分你提交的数据不同的选项的 value 值要控制为不同，这样后台接收就可以区分了。

这里同样是 checked 可以简写，表示默认选中，也可以写为：

```
checked="checked"
```

value 属性值代表它们的值。

### 4.1.3 多选按钮

多选按钮也需要使用 name 属性控制分组，type 属性设置为 **checkbox**，value 属性代表它们的值：

```html
 你喜欢的语言：
<input type="checkbox" name="favlan" value="1" checked="checked"/>java
<input type="checkbox" name="favlan" value="2" checked="checked"/>python
<input type="checkbox" name="favlan" value="3"/>php
<input type="checkbox" name="favlan" value="4"/>c#
```

### 4.1.4 文件上传

type 属性设置为 **file** 可以使用文件上传按钮：

```html
<input type="file" />
```

然后选择文件上传。

### 4.4.5 下拉框







### 4.4.6 文本域





### 4.4.7 label







### 4.4.8 提交





### 4.4.9 其他

**隐藏域：**

```html
<input type="hidden" name="uname6" value="123123">
```

 









## 4.2 html5新增type类型











## 4.3 html5新增属性





















