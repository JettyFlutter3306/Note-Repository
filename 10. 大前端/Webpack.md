# Webpack

# 一. 前端工程化

## 1.1 现代化前端开发

现代化的前端开发：

- 模块化(js 的模块化、css 的模块化、资源的模块化)
- 组件化(复用现有的 UI 结构、样式、行为)
- 规范化(目录结构的划分、编码规范化、接口规范化、文档规范化、 Git 分支管理)
- 自动化(自动化构建、自动部署、自动化测试)

## 1.2 什么是前端工程化

前端工程化指的是：在企业级的前端项目开发中，把前端开发所需的工具、技术、流程、经验等进行规范化、 标准化。

企业中的 Vue 项目和 React 项目，都是基于工程化的方式进行开发的。

好处:前端开发自成体系，有一套标准的开发方案和流程。

## 1.3 工程化解决方案

早期的前端工程化解决方案:

- **Grunt** (https://www.gruntjs.net/ )
- **Gulp** (https://www.gulpjs.com.cn/)

目前主流的前端工程化解决方案:

- **webpack** (https://www.webpackjs.com/) 
- **parcel** (https://zh.parceljs.org/)

# 二. webpack使用

## 2.1 什么是webpack

概念：webpack 是前端项目工程化的具体解决方案。

主要功能：它提供了友好的前端模块化开发支持，以及代码压缩混淆、处理浏览器端 JavaScript 的兼容性、性能优化等强大的功能。

好处：让程序员把工作的重心放到具体功能的实现上，提高了前端开发效率和项目的可维护性。 注意:目前 Vue，React 等前端项目，基本上都是基于 webpack 进行工程化开发的。

## 2.2 安装webpack

在终端运行如下的命令，安装 webpack 相关的两个包: 

```bash
npm install webpack@5.42.1 webpack-cli@4.7.2 -D
```

## 2.3 配置webpack

1. 在项目根目录中，创建名为 webpack.config.js 的 webpack 配置文件，并初始化如下的基本配置:

```js
module.export = {
  mode: 'development'  // mode用来指定模式，可选值为 development、production
}
```

2. 在 package.json 的 scripts 节点下，新增 dev 脚本如下:

```js
"scripts": {
  "dev": "webpack"  // 可执行的脚本
}
```

3. 在终端中运行 

```bash
npm run dev
```

命令，启动 webpack 进行项目的打包构建

### 2.3.1 mode可选值

mode 节点的可选值有两个，分别是:

1. **development**

**开发环境**，不会对打包生成的文件进行代码压缩和性能优化，打包速度快，适合在开发阶段使用。

2. **production**

**生产环境**，会对打包生成的文件进行代码压缩和性能优化，打包速度很慢，仅适合在项目发布阶段使用。

### 2.3.2 **webpack.config.js**文件的作用

webpack.config.js 是 webpack 的配置文件。webpack 在真正开始打包构建之前，会先读取这个配置文件， 从而基于给定的配置，对项目进行打包。

注意:由于 webpack 是基于 node.js 开发出来的打包工具，因此在它的配置文件中，支持使用 node.js 相关 的语法和模块进行 webpack 的个性化配置。

### 2.3.3 webpack中的默认约定

在 webpack 4.x 和 5.x 的版本中，有如下的默认约定: 

1. 默认的打包入口文件为 **src -> index.js**
2. 默认的输出文件路径为 **dist -> main.js**

注意:可以在 webpack.config.js 中修改打包的默认约定。

### 2.3.4 自定义打包的入口与出口

在 webpack.config.js 配置文件中，通过 entry 节点指定打包的入口。通过 output 节点指定打包的出口。 

示例代码如下:

```js
const path = require('path')  // 导入node中专门操作路径的模块
module.exports = {
	entry: path.join(__dirname, './src/index.js'),  // 打包入口文件的路径
  output: {
    path: path.join(__dirnamem, './dist'),  // 输出文件的存放路径
    filename: 'bundle.js'  // 输出文件名称
  }
}
```

# 三. webpack插件

## 3.1 webpack插件的作用

通过安装和配置第三方的插件，可以拓展 webpack 的能力，从而让 webpack 用起来更方便。最常用的 webpack 插件有如下两个:

1. **webpack-dev-server**

类似于 node.js 阶段用到的 nodemon 工具。每当修改了源代码，webpack 会自动进行项目的打包和构建。

2. **html-webpack-plugin**

 webpack 中的 HTML 插件(类似于一个模板引擎插件)，可以通过此插件自定制 index.html 页面的内容。

## 3.2 webpack-dev-server

webpack-dev-server 可以让 webpack 监听项目源代码的变化，从而进行自动打包构建。

安装**webpack-dev-server**，运行如下的命令，即可在项目中安装此插件:

```bash
npm install webpack-dev-server@3.11.2 -D
```

配置webpack-dev-server：

1. 修改 package.json -> scripts 中的 dev 命令如下:

```bash
"scripts": {
	"dev": "webpack serve"
}
```

2. 再次运行 `npm run dev` 命令，重新进行项目的打包

3. 在浏览器中访问 http://localhost:8080 地址，查看自动打包效果

**注意:webpack-dev-server 会启动一个实时打包的 http 服务器**

**打包生成的文件哪儿去了?**

1. 不配置 webpack-dev-server 的情况下，webpack 打包生成的文件，会存放到实际的物理磁盘上。严格遵守开发者在 webpack.config.js 中指定配置。根据 output 节点指定路径进行存放。
2. 配置了 webpack-dev-server 之后，打包生成的文件存放到了内存中。不再根据 output 节点指定的路径，存放到实际的物理磁盘上。提高了实时打包输出的性能，因为内存比物理磁盘速度快很多

**生成到内存中的文件该如何访问?**

webpack-dev-server 生成到内存中的文件，默认放到了项目的根目录中，而且是虚拟的、不可见的。可以直接用 / 表示项目根目录，后面跟上要访问的文件名称，即可访问内存中的文件。例如 /bundle.js 就表示要访问 webpack-dev-server 生成到内存中的 bundle.js 文件。

## 3.3 html-webpack-plugin

html-webpack-plugin 是 webpack 中的 HTML 插件，可以通过此插件自定制 index.html 页面的内容。 需求:通过 html-webpack-plugin 插件，将 src 目录下的 index.html 首页，复制到项目根目录中一份!

**安装** **html-webpack-plugin**，运行如下的命令，即可在项目中安装此插件:

```bash
npm install html-webpack-plugin@5.3.2 -D
```

**配置** **html-webpack-plugin**：

```js
// 引入html插件
const HTMLWebpackPlugin = require('html-webpack-plugin')

// 创建html插件的实例对象
const htmlPlugin = new HtmlPlugin({
  template: './src/index.html',  // 指定原文件存放的路径
  filename: './index.html'  // 指定生成的文件的存放路径
})

module.exports = {
  mode: 'development',
  plugins: [htmlPlugin]  // 使得htmlPlugin生效
}
```

**解惑** **html-webpack-plugin**

1. 通过 HTML 插件复制到项目根目录中的 index.html 页面，也被放到了内存中 
2. HTML 插件在生成的 index.html 页面，自动注入了打包的 bundle.js 文件

## 3.4 devServer节点

在 webpack.config.js 配置文件中，可以通过 devServer 节点对 webpack-dev-server 插件进行更多的配置：

```js
devServer: {
  open: true,  // 初次打包完成后，自动打开浏览器
  host: '127.0.0.1',  // 实时打包所使用IP地址
  port: 80  // 端口号
}
```

注意:凡是修改了 webpack.config.js 配置文件，或修改了 package.json 配置文件，必须重启实时打包的服 务器，否则最新的配置文件无法生效!

# 四. webpack的loader

## 4.1 loader概述

在实际开发过程中，webpack 默认只能打包处理以 .js 后缀名结尾的模块。其他非 .js 后缀名结尾的模块， webpack 默认处理不了，需要调用 loader 加载器才可以正常打包，否则会报错!

loader 加载器的作用:协助 webpack 打包处理特定的文件模块。比如: 

- css-loader 可以打包处理 .css 相关的文件
- less-loader 可以打包处理 .less 相关的文件
- babel-loader 可以打包处理 webpack 无法处理的高级 JS 语法

## 4.2 loader的调用过程

webpack 将要打包处理的文件模块先判断是否是 js 模块，然后判断是否包含了js 高级语法或者对应的 loader。包含了 js 高级语法，那么就去判断是否配置了 Babel loader，有的话就去调用 Babel loader，没有高级语法那就 webpack 自己处理。配置了对应的 loader 那就调用对应的 loader 处理。

## 4.3 打包处理css文件

运行

```bash
npm i style-loader@3.0.0 css-loader@5.2.6 -D 
```

命令，安装处理 css 文件的 loader

在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下:

```js
module: {  // 所有第三方文件模块的匹配规则
  rules: [  // 文件后缀名匹配规则
    { test: /\.css$/, use: ['style-loader', 'css-loader'] }
  ]
}
```

其中，test 表示匹配的文件类型， use 表示对应要调用的 loader。

注意:

- use 数组中指定的 loader 顺序是固定的。
- 多个 loader 的调用顺序是:从后往前调用。

### 4.4 打包less文件

运行命令：

```bash
npm i less-loader@10.0.1 less@4.1.1 -D
```

在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下:

```js
module: {
  rules: [
    { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] }
  ]
}
```

## 4.5 打包处理样式表中url相关文件

运行命令：

```bash
npm i url-loader@4.1.1 file-loader@6.2.0 -D
```

在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下:

```js
module: {
  rules: [
    { test: /\.jpg|png|gif$/, use: 'url-loader?limit=22229' }
  ]
}
```

其中 ? 之后的是 loader 的参数项:

1. limit 用来指定图片的大小，单位是字节(byte)
2.  只有 ≤ limit 大小的图片，才会被转为 base64 格式的图片

## 4.6 打包高级语法js文件

webpack 只能打包处理一部分高级的 JavaScript 语法。对于那些 webpack 无法处理的高级 js 语法，需要借 助于 babel-loader 进行打包处理。例如 webpack 无法处理下面的 JavaScript 代码:

```js
function info(target) {
  target.info = 'Person Info'
}

@info
class Person {
  
}

console.log(Person.info)
```

运行命令：

```bash
npm i babel-loader@8.2.2 @babel/core@7.14.6 @babel/plugin-proposal-decorators@7.14.5 -D
```

在 webpack.config.js 的 module -> rules 数组中，添加 loader 规则如下:

```js
{ test: /\.js$/, use: 'babel-loader', exclude: /node_modules/ }
```

### 4.6.1 配置babel-loader

在项目根目录下，创建名为 babel.config.js 的配置文件，定义 Babel 的配置项如下:

```js
module.exports = {
  plugins: [['@babel/plugin-proposal-decorator', { legacy: true }]]
}
```

# 五. 打包发布

## 5.1 为什么打包发布

项目开发完成之后，需要使用 webpack 对项目进行打包发布，主要原因有以下两点:

1. 开发环境下，打包生成的文件存放于内存中，无法获取到最终打包生成的文件
2. 开发环境下，打包生成的文件不会进行代码压缩和性能优化 

为了让项目能够在生产环境中高性能的运行，因此需要对项目进行打包发布。

## 5.2 配置webpack打包发布

在 package.json 文件的 scripts 节点下，新增 build 命令如下:

```js
"scripts": {
  "dev": "webpack serve",
  "buidld": "webpack --mode production"
}
```

--model 是一个参数项，用来指定 webpack 的运行模式。production 代表生产环境，会对打包生成的文件 进行代码压缩和性能优化。

注意:通过 --model 指定的参数项，会覆盖 webpack.config.js 中的 model 选项。

## 5.3 把js文件统一打包到js目录

在 webpack.config.js 配置文件的 output 节点中，进行如下的配置:

```js
output: {
  path: path.join(__dirname, 'dist'),
  filename: 'js/bundle.js'
}
```

## 5.4 图片文件统一生成到image目录中

修改 webpack.config.js 中的 url-loader 配置项，新增 outputPath 选项即可指定图片文件的输出路径:

```js
{
  test: /\.jpg|png|gif$/,
  use: {
    loader: 'url-loader',
    options: {
      limit: 22228,
      outputPath: 'image'
    }
  }
}
```

## 5.5 自动清理dist

为了在每次打包发布时自动清理掉 dist 目录中的旧文件，可以安装并配置 clean-webpack-plugin 插件:

安装命令：

```bash
npm install clean-webpack-plugin@3.0.0 -D
```

```js
// 引入clean插件
const {CleanWebpackPlugin} = require('clean-webpack-plugin')
const cleanPlugin = new CleanWebpackPlugin()

// 挂载到plugins节点
plugins: [htmlPlugin, cleanPlugin]
```

# 六. SourceMap

## 6.1 生产环境遇到问题

前端项目在投入生产环境之前，都需要对 JavaScript 源代码进行压缩混淆，从而减小文件的体积，提高文件的 加载效率。此时就不可避免的产生了另一个问题:

对压缩混淆之后的代码除错(debug)是一件极其困难的事情。

 变量被替换成没有任何语义的名称，空行和注释被剔除。

## 6.2 什么是SourceMap

Source Map 就是一个信息文件，里面储存着位置信息。也就是说，Source Map 文件中存储着压缩混淆后的 代码，所对应的转换前的位置。

有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码，能够极大的方便后期的调试。

## 6.3 webpack开发环境下SourceMap

在开发环境下，webpack 默认启用了 Source Map 功能。当程序运行出错时，可以直接在控制台提示错误行 的位置，并定位到具体的源代码。

### 6.3.1 默认SourceMap问题

开发环境下默认生成的 Source Map，记录的是生成后的代码的位置。会导致运行时报错的行数与源代码的行 数不一致的问题。

### 6.3.2 解决默认SourceMap问题

开发环境下，推荐在 webpack.config.js 中添加如下的配置，即可保证运行时报错的行数与源代码的行数保持一致:

```js
module.exports = {
  mode: 'development',
  // eval-source-map 仅限在开发模式下使用
  devtool: 'eval-source-map'
}
```

## 6.4 webpack生产环境下SourceMap

在生产环境下，如果省略了 devtool 选项，则最终生成的文件中不包含 Source Map。这能够防止原始代码通 过 Source Map 的形式暴露给别有所图之人。

### 6.4.1 只定位行数不暴露源码

在生产环境下，如果只想定位报错的具体行数，且不想暴露源码。此时可以将 devtool 的值设置为 **nosources-source-map**。

### 6.4.2 定位行数且暴露源码

在生产环境下，如果想在定位报错行数的同时，展示具体报错的源码。此时可以将 devtool 的值设置为 **source-map**。

## 6.5 SourceMap最佳实践

1.  开发环境下:

- 建议把 devtool 的值设置为 eval-source-map
- 好处:可以精准定位到具体的错误行

2. 生产环境下:

- 建议关闭 Source Map 或将 devtool 的值设置为 nosources-source-map
- 好处:防止源码泄露，提高网站的安全性
