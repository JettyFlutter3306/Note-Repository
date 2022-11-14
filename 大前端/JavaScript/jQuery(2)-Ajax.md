# jQuery(2)-Ajax

# 一. 简介

## 1.1 简介

**AJAX** 就是 **Asynchronous Javascript And XML**（异步 JavaScript 和 XML），是指一种创建交互式、快速动态网页应用的网页开发技术，无需重新加载整个网页的情况下，能够更新部分网页的技术。通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

**最大特点：**异步访问，局部刷新。

## 1.2 快速使用

以访问用户名是否存在为例：

```js
const xhr = new XMLHttpRequest()
// 使用xhr对象设置打开链接
// 设置请求方式和参数xhr.open("请求方式","请求的URL",是否使用异步方式)
xhr.open("GET", "user/select", true)
// 设置回调函数
xhr.onreadystatechange = function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
        const result = xhr.responseText
        console.log(result)  // 查询所有用户数据
    }
}
// 正式发送请求
xhr.send(null)
```

AJAX 异步提交请求的步骤为：

1. 获取 XMLHTTPRequest 对象。
2. 打开链接 `xhr.open`。
3. 设置回调函数 `xhr.onreadystatechange`。
4. 提交数据 `xhr.send(data)`。

原生 js 提交 AJAX 异步请求代码比较繁琐，处理复杂数据比较麻烦,后续可以使用 jQuery 解决。

如果响应的数据是一个对象或者对象集合，数据处理起来会非常麻烦，可以使用 JSON 格式处理。

# 二. jQuery的Ajax

## 2.1 快速使用

每次书写 AJAX 代码比较繁琐 步骤都是一样的，数据回显使用原生 js 代码也比较繁琐，可以使用 jQuery 对上述问题进行优化，jQuery 不仅仅对 DOM 操作进行了封装，同时也对 AJAX 提交和回显已经进行了封装，可大大简化 AJAX 的操作步骤。

**jQuery.ajax() 简单使用：**

```js
```







## 2.2 属性介绍









## 2.3 其他写法







## 2.4 jsonp跨域处理







# 三. 三级联动案例











