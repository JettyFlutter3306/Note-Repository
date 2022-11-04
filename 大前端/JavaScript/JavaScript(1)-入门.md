# JavaScript-入门篇

# 一. 快速入门

## 1.1 JS简介

Javascript 是一种由Netscape(网景)的 LiveScript 发展而来的原型化继承的面向对象的动态类型的区分大小写的客户端脚本语言，主要目的是为了解决服务器端语言，比如 Perl，遗留的速度问题，为客户提供更流畅的浏览效果。当时服务端需要对数据进行验证，由于网络速度相当缓慢,只有28.8kbps，验证步骤浪费的时间太多。于是Netscape的浏览器Navigator加入了Javascript，提供了数据验证的基本功能。JavaScript 的正式名称是 "ECMAScript"。这个标准由 ECMA 组织发展和维护。ECMA-262 是正式的 JavaScript 标准。这个标准基于 JavaScript (Netscape) 和 JScript (Microsoft)。Netscape (Navigator 2.0) 的 Brendan Eich 发明了这门语言，从 1996 年开始，已经出现在所有的 Netscape 和 Microsoft 浏览器中。ECMA-262 的开发始于 1996 年，在 1997 年 7 月，ECMA 会员大会采纳了它的首个版本。JavaScript 的组成包含 ECMAScript、DOM、BOM。JS 是一种运行于浏览器端上的小脚本语句,可以实现网页如文本内容动,数据动态变化和动画特效等。

**ECMAScript：**是一种由欧洲计算机制造商协会（ECMA）通过 ECMA-262 标准化的脚本程序语言,简单点说，ECMAScript 描述了语法、类型、语句、关键字、保留字、运算符和对象。它就是定义了脚本语言的所有属性、方法和对象。

**DOM：**把整个页面规划成由节点层构成的文档，它不与浏览器、平台、语言相关，为web开发者提供一个标准可以访问站点中的数据、脚本和表现层对象。DOM 编程可以实现网页内容校验和动态变化的效果。

**BOM：**是浏览器的一种特性，它可以对浏览器窗口进行访问和操作，例如移动、关闭窗口，调整窗口的大小，支持 cookie 等。BOM 编程可以实现动态控制浏览器本身行为的效果。

## 1.2 JS特点

1. 脚本语言

脚本语言是一种简单的程序，规模小,不需要编译,运行快,是由一些 ASCII 字符构成，可以使用任何一种文本编辑器编写。脚本语言是指在 web 浏览器内有解释器解释执行的编程语言，每次运行程序的时候，解释器会把程序代码翻译成可执行的格式。一些程序语言（如C、C++、Java等）都必须经过编译，将源代码编译成二进制的可执行文件之后才能运行，而脚本语言不需要事先编译，只要有一个与其相适应的解释器就可以执行。

2. 基于对象的语言

面向对象有三大特点（封装，继承，多态）缺一不可。通常"基于对象"是使用对象，但是无法利用现有的对象模板产生新的对象类型，也就是说"基于对象"没有继承的特点。没有了继承的概念也就无从谈论"多态"。

3. 事件驱动

在网页中执行了某种操作的动作，被称为"事件"(Event)，比如按下鼠标、移动窗口、选择菜单等都可以视为事件。当事件发生后，可能会引起相应的事件响应。

4. 简单性

变量类型是采用弱类型，并未使用严格的数据类型。var a,b,c; a=123; b="abc"; a=b; 

5. 安全性

JavaScript 不能访问本地的硬盘，不能将数据存入到服务器上，不能对网络文档进行修改和删除，只能通过浏览器实现信息浏览或动态交互。

6. 跨平台性

JavaScript 依赖于浏览器本身，与操作平台无关， 只要计算机安装了支持 JavaScript 的浏览器（装有JavaScript解释器)，JavaScript 程序就可以正确执行。

缺点：

各种浏览器支持 JavaScript 的程度是不一样的，支持和不完全支持 JavaScript的 浏览器在浏览同一个带有JavaScript 脚本的网页时，效果会有一定的差距，有时甚至会显示不出来。

基于对象和面向对象：JavaScript 是脚本语言，是一种基于对象的语言。本身提供了非常丰富的内部对象供设计人员使用，但不支持继承和多态。Java 是面向对象的，是一种真正的面向对象的语言，支持封装、继承和多态。

## 1.3 JS引入方式

1. 内嵌式：

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title></title>
    </head>
    <body>
        <input type="button" value="点我呀" onclick="fun1()">
    </body>
</html>
<script type="text/javascript">
/*定义一个函数(方法)*/
function fun1() {
    /*弹窗提示一点信息 */
    alert("你好")
}
</script>
```

2. 外链式：

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title></title>
        <script type="text/javascript" src="js/myjs.js"></script>
    </head>
    <body>
        <input type="button" value="点我呀" onclick="fun1()">
    </body>
</html>
```

使用 script 标签引入外部 js 文件。**编写 js 语句时，可以不加分号，但是要换行。**

# 二. 数据类型和运算符

## 2.1 数据类型

1. 数值型：number 整数和浮点数统称为数值。
2. 字符串：string 由0个,1个或多个字符组成的序列。在 JavaScript 中，单引号或者双引号包裹的内容都是字符串类型。
3. 布尔型：boolean 用 true 或 false 来表示。
4. null：表示没有值，用于定义空的或不存在的引用。要注意，空值不等同于空字符串 "" 或 0。当给一个变量赋值为 null 时,变量的类型为 object。
5. undefined：它也是一个保留字。表示变量虽然已经声明，但却没有赋值。
6. object：包括对象和数组。
7. 函数：指函数数据类型，使用关键字 function 声明。

无论是什么数据类型，统一都使用关键字 `var` 声明变量：

```js
var a = 10  
console.log(typeof a)  // number
var b = 'aaa'  
console.log(typeof b)  // string
var c = false  
console.log(typeof c)  // boolean
var d = null  
console.log(typeof d)  // object
var e
console.log(typeof e)  // undefined
var f = {}
console.log(typeof f)  // object
var g = []
console.log(typeof g)  // object
var h = function() {}
console.log(typeof h)  // function
```

## 2.2 运算符号

JS 中运算符号大部分和 Java 中的运算符一样，我们在这里分析一下特殊的运算符号：

| 类型       | 运算符                            |
| ---------- | --------------------------------- |
| 算术运算符 | `+` `-` `*` `/` `%` `++` `--`     |
| 赋值运算符 | `=`                               |
| 比较运算符 | `>` `<` `>=` `<=` `==` `===` `!=` |
| 逻辑运算符 | `&&` `||` `!`                     |

**/ % 运算：**

```js
console.log(10 / 3)    // 3.3333...  除尽就是整数，除不尽就是浮点数
console.log(10 / 0)    // Infinity
console.log(10 % 3)    // 1
console.log(10 % 0)    // NaN
console.log(10 % 1.5)  // 1
```

JS 取余数运算对于浮点数仍然有效,如果和0取余数,结果是 NaN(not a number)。

**+ 运算：**

\+ 同时也是连接运算符,看两端的变量类型,如果都是 number 那么就是算数中的加法 如果有字符串,那么就是连接符号,如果是布尔类型和 number 相加,那么会将 true 转化为 1 将 false 转化为 0。

```js
console.log(1 + 1)     // 2
console.log(1 + "1")   // 11(string)
console.log(1 + true)  // 2
```

**== 等值符号：**

先比较类型,如果类型一致，再比较内容，如果类型不一致,会强制转换为 number 再比较内容：

```js
console.log(1 == "1")        // true
console.log(1 == true)       // true  
console.log(1 == "true")     // false  
console.log("1" == true)     // true  
console.log("1" == "true")   // false
console.log(true == "true")  // false
```

**=== 等值符号：**

数据类型不同，直接返回 false 如果类型相同，才会比较内容：

```js
console.log(1 === "1")       // false
console.log(1 === true)      // false
console.log(1 === "true")     // false
console.log("1" === true)     // false
console.log("1" === "true")   // false
console.log(true === "true")  // false
console.log("123" === "123")
```

**字符串运算：**

字符串类型在做算数运算时会做隐式转换成 number，最终的结果也是 number：

```js
console.log("1" * 2)  // 2
console.log("1" / 2)  // 0.5
console.log("1" - 1)  // 0
console.log("1" % 2)  // 1
console.log("1" << 1) // 2 
```

## 2.3 流程控制

### 2.3.1 分支结构

**if分支：**

```js
var a = 10
if (a >= 12) {
    console.log("...")
} else if (a >= 5) {
    console.log("...")
} else {
    console.log("...")
}
```

**switch分支：**

```js
var a = 10
switch (a) {
    case 1:
    case 2:
    case 3:
        console.log("...")
        break
    case 4:
    case 5:
        console.log("...")
        break
    default:
        console.log("...")
}
```

### 2.3.2 循环结构

**while循环：**

```js
var a = 0
while (a < 10) {
    a++
}
```

**do-while循环：**

```js
var a = 0
do {
    a++
} while (a < 10)
```

**for循环：**

```js
for (var i = 0; i < 5; i++) {
    console.log(i)
}
```

## 2.4 函数

声明函数的三种方式：

1. 使用关键字 `function` 直接声明：

```js
function fun() {
    // ...
}
```

2. 函数变量：

```js
var a = function() {
    // ...
}
```

3. 使用关键字 `new`：

```js
// 括号内传入js代码
var b = new Function("...")
```

**参数和返回值问题：**

```js
// 传入的参数个数可以和参数列表个数不一致
function fun(a, b, c) {
    // 如果有返回值则使用return返回，没有则不用
    return a + b
}
console.log(fun(1, 2))  // 3

// 参数为函数类型，也就是回调函数
function fun(arr, f) {
    return f(arr)
}
var arr = [1, 2, 3, 4, 5]
console.log(fun(arr, function(arr) {
    return arr.reduce((a, b) => a + b)
}))  // 15
```

# 三. 数组

## 3.1 声明方式

四种创建方式：

1. 创建空数组：

```js
var arr = new Array()
arr[0] = 1
console.log(arr)
```

2. 创建定长数组：

```js
var arr = new Array(5)
arr[0] = 1
arr[2] = "abc"
```

3. 创建时指定元素值：

```js
var arr = new Array("asdf", 10, 20.3, true)
```

4. 直接声明：

```js
var arr = ["asdf", 10, 20.3, true]
```

最常用的是第四种。

## 3.2 元素和长度

```js
var arr = [1, 2, 3, 4, 5]
// 获取长度
console.log(arr.length)

// JS中的数组是可以通过修改length属性来改变数组长度的
arr.length = 10
console.log(arr)

// JS的数组可以通过索引改变数组的长度
arr[9] = 100
console.log(arr)
```

## 3.3 遍历方式

1. 普通for循环：

```js
for (var i = 0; i < arr.length; i++) {
    console.log(arr[i])
}
```

2. for...in：

```js
// 这里的i指的是索引
for (var i in arr) {
    console.log(arr[i])
}
```

3. for...of

```js
// 这里的i指的是元素
for (var i of arr) {
    console.log(i)
}
```

## 3.4 常用方法

1. **indexOf**

```js
var arr = [1, 2, 3, 4, 5, 7, 8, 9]
// 查询元素索引
console.log(arr.indexOf(7))  // 5
```

2. **concat**

```js
var arr1 = [1, 2, 3]
var arr2 = [2, 4, 5]
console.log(arr1.concat(arr2))  // [1, 2, 3, 2, 4, 5]
// concat还可以合并多个数组，只要传递参数即可
var arr3 = [7, 8, 9]
arr1.concat(arr2, arr3)
```

3. **join**

```js
var fruits = ["Banana", "Orange", "Apple", "Mango"];
// 合并字符串，默认使用逗号隔开
console.log(fruits.join())  // Banana,Orange,Apple,Mango
// 可以使用其他分隔符隔开
console.log(fruits.join("."))  // Banana.Orange.Apple.Mango
```

4. **pop**

```js
var arr = [1, 2, 3, 4, 5]
// 弹出最后一个元素
console.log(arr.pop())  // 5
```

5. **push**

```js
var arr = [1, 2, 3, 4, 5]
// 尾插入数据
arr.push(6)
console.log(arr)  // [1, 2, 3, 4, 5, 6]
```

6. **reverse**

```js
var arr = [1, 2, 3, 4, 5]
// 逆转数组
arr.reverse()
console.log(arr)  // [5, 4, 3, 2, 1]
```

7. **shift**

```js
var arr = [1, 2, 3, 4, 5]
// 删除数组第一个元素
console.log(arr.shift())  // 1
```

8. **unshift**

```js
var arr = [1, 2, 3, 4, 5]
// 向第一个位置添加元素
arr.unshift(7)
console.log(arr)  // [7, 5, 4, 3, 2, 1]
```

9. **slice**

```js
var arr = [1, 2, 3, 4, 5]
// 截取索引[1, 3)
console.log(arr.slice(1, 3))  // [2, 3]
```

10. **splice**

```js
var arr = [1, 2, 3, 4, 5]
// 删除元素从指定索引开始，第二个参数指定数量
arr.splice(1, 3)
console.log(arr)  // [1, 5]
```

11. **sort**

```js
var arr = [8, 5, 1, 9, 6]
// 默认升序排序
console.log(arr.sort())  // [1, 5, 6, 8, 9]
// 传入回调函数降序排序
console.log(arr.sort((a, b) => b - a))  // [9, 8, 6, 5, 1]
```

# 四. 对象











# 五. 事件













# 六. BOM







# 七. DOM







