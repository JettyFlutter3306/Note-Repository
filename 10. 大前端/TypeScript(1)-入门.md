# TypeScript(1)

# 一. TypeScript简介

1. TypeScript 是 JavaScript 的超集。
2. 它对 JS 进行了扩展，向 JS 中引入了类型的概念，并添加了许多新的特性。
3. TS 代码需要通过编译器编译为JS，然后再交由JS解析器执行。
4. TS 完全兼容 JS，换言之，任何的 JS 代码都可以直接当成 JS 使用。
5. 相较于 JS 而言，TS 拥有了静态类型，更加严格的语法，更强大的功能；TS 可以在代码执行前就完成代码的检查，减小了运行时异常的出现的几率；TS 代码可以编译为任意版本的 JS 代码，可有效解决不同JS 运行环境的兼容问题；同样的功能，TS 的代码量要大于JS，但由于TS的代码结构更加清晰，变量类型更加明确，在后期代码的维护中TS却远远胜于 JS。

# 二. 环境搭建

1. 下载 Node.js
2. 安装 Node.js
3. 使用 npm 全局安装 TypeScript：

进入终端，输入：

```shell
npm i typescript -g
```

4. 创建一个 ts 文件
5. 使用 `tsc` 命令对 ts 文件进行编译：

```shell
tsc index.ts
```

# 三. 基本类型

## 3.1 类型声明

类型声明是 TS 非常重要的一个特点，通过类型声明可以指定TS中变量（参数、形参）的类型，指定类型后，当为变量赋值时，**TS 编译器会自动检查值是否符合类型声明**，符合则赋值，否则报错。简而言之，类型声明给变量设置了类型，使得变量只能存储某种类型的值。

语法：

```typescript
let var1: type
let var2: type = val

function fn(var1: type1, var2: type2): type3 {
    // do something
}
```

## 3.2 自动类型判断

TS 拥有自动的类型判断机制，当对变量的声明和赋值是同时进行的，**TS编译器会自动判断变量的类型**，所以如果你的变量的声明和赋值时同时进行的，**可以省略掉类型声明**。

## 3.3 类型列表

|  类型   |       例子        |              描述              |
| :-----: | :---------------: | :----------------------------: |
| number  |    1, -33, 2.5    |            任意数字            |
| string  | 'hi', "hi", `hi`  |           任意字符串           |
| boolean |    true、false    |       布尔值true或false        |
| 字面量  |      其本身       |  限制变量的值就是该字面量的值  |
|   any   |         *         |            任意类型            |
| unknown |         *         |         类型安全的any          |
|  void   | 空值（undefined） |     没有值（或undefined）      |
|  never  |      没有值       |          不能是任何值          |
| object  |  {name:'孙悟空'}  |          任意的JS对象          |
|  array  |      [1,2,3]      |           任意JS数组           |
|  tuple  |       [4,5]       | 元素，TS新增类型，固定长度数组 |
|  enum   |    enum{A, B}     |       枚举，TS中新增类型       |

### 3.3.1 number

```typescript
let decimal: number = 6
let hex: number = 0xf00d
let binary: number = 0b1010
let octal: number = 0o744
let big: bigint = 100n
```

### 3.3.2 string

```typescript
let color: string = "blue"
color = 'red'

let fullName: string = `Bob Bobbington`
let age: number = 37
let sentence: string = `Hello, my name is ${fullName}.
I'll be ${age + 1} years old next month.`
```

### 3.3.3 boolean

```typescript
let isDone: boolean = false;
```

### 3.3.4 字面量

也可以使用字面量去指定变量的类型，通过字面量可以确定变量的取值范围。

```typescript
let color: 'red' | 'blue' | 'black'
let num: 1 | 2 | 3 | 4 | 5
```

### 3.3.5 any

```typescript
let d: any = 4
d = 'hello'
d = true
```

### 3.3.6 unkown

```typescript
let notSure: unknown = 4;
notSure = 'hello';
```

### 3.3.7 void

```typescript
let unusable: void = undefined
```

### 3.3.8 never

```typescript
function error(message: string): never {
  throw new Error(message)
}
```

### 3.3.9 object

object 类型确实没啥用处。

```typescript
let obj: object = {};
```

### 3.3.10 array

```typescript
// 第一种方式
let list: number[] = [1, 2, 3]

// 第二种方式
let list: Array<number> = [1, 2, 3]
```

### 3.3.11 tuple

```typescript
let x: [string, number]
x = ["hello", 10]
```

### 3.3.12 enum

```typescript
enum Color {
  Red,
  Green,
  Blue,
}
let c: Color = Color.Green

enum Color {
  Red = 1,
  Green,
  Blue,
}
let c: Color = Color.Green

enum Color {
  Red = 1,
  Green = 2,
  Blue = 4,
}
let c: Color = Color.Green
```

## 3.4 类型断言

有些情况下，变量的类型对于我们来说是很明确，但是TS编译器却并不清楚，此时，可以通过类型断言来告诉编译器变量的类型，断言有两种形式：

第一种：

```typescript
let someValue: unknown = "this is a string"
let strLength: number = (someValue as string).length
```

第二种：

```typescript
let someValue: unknown = "this is a string"
let strLength: number = (<string>someValue).length
```

# 四. 编译选项

## 4.1 自动编译文件

编译文件时，使用 `-w` 指令后，TS编译器会自动监视文件的变化，并在文件发生变化时对文件进行重新编译。

```shell
tsc index.ts -w
```

## 4.2 自动编译整个项目

如果直接使用 tsc 指令，则可以自动将当前项目下的所有ts文件编译为 js 文件。但是能直接使用 tsc 命令的前提时，要先在项目根目录下创建一个 ts 的配置文件 **tsconfig.json**。**tsconfig.json** 是一个 **JSON** 文件，添加配置文件后，只需只需 tsc 命令即可完成对整个项目的编译。

配置选项：

### 4.2.1 include

1. 定义希望被编译文件所在的目录。
2. 默认值：["\*\*/\*"]

3. 示例：

```json
"include":["src/**/*", "tests/**/*"]
```

上述示例中，**所有 src 目录和 tests 目录下的文件都会被编译**。

### 4.2.2 exclude

1. 定义需要排除在外的目录。
2. 默认值：["node_modules",  "bower_components",  "jspm_packages"]

3. 示例：

```json
"exclude": ["./src/hello/**/*"]
```

上述示例中，**src 下 hello 目录下的文件都不会被编译**。

### 4.2.3 extends

1. 定义被继承的配置文件。
2. 示例：

```json
"extends": "./configs/base"
```

上述示例中，当前配置文件中会自动包含config目录下 base.json 中的所有配置信息。

### 4.2.4 files

指定被编译文件的列表，只有需要编译的文件少时才会用到。

示例：

```json
"files": [
    "core.ts",
    "sys.ts",
    "types.ts",
    "scanner.ts",
    "parser.ts",
    "utilities.ts",
    "binder.ts",
    "checker.ts",
    "tsc.ts"
]
```

### 4.2.5 compilerOptions

编译选项是配置文件中非常重要也比较复杂的配置选项，在 compilerOptions 中包含多个子选项，用来完成对编译的配置。

选项列表：

- target：**设置 ts 代码编译的目标版本**。可选值：ES3（默认）、ES5、ES6/ES2015、ES7/ES2016、ES2017、ES2018、ES2019、ES2020、ESNext。

```json
"compilerOptions": {
    "target": "ES6"
}
```

编写的 ts 代码将会被编译为 ES6 版本的 js 代码。

- lib：**指定代码运行时所包含的库(宿主环境)**。可选值：ES5、ES6/ES2015、ES7/ES2016、ES2017、ES2018、ES2019、ES2020、ESNext、DOM、WebWorker、ScriptHost ......

```json
"compilerOptions": {
    "target": "ES6",
    "lib": ["ES6", "DOM"],
    "outDir": "dist",
    "outFile": "dist/aa.js"
}
```

- module：**设置编译后代码使用的模块化系统**。可选值：CommonJS、UMD、AMD、System、ES2020、ESNext、None。

```json
"compilerOptions": {
    "module": "CommonJS"
}
```

- outDir：**编译后文件的所在目录**。默认情况下，编译后的js文件会和ts文件位于相同的目录，设置outDir后可以改变编译后文件的位置。

```json
"compilerOptions": {
    "outDir": "dist"
}
```

**设置后编译后的 js 文件将会生成到 dist 目录**。

- outFile：**将所有的文件编译为一个js文件**。默认会将所有的编写在全局作用域中的代码合并为一个 js 文件，如果 module 制定了 None、System 或 AMD 则会将模块一起合并到文件之中。

```json
"compilerOptions": {
    "outFile": "dist/app.js"
}
```

- allowJs：是否对js文件编译。
- checkJs：是否对js文件进行检查。

```json
"compilerOptions": {
    "allowJs": true,
    "checkJs": true
}
```

- removeComments：是否删除注释。默认值：false。
- noEmit：不对代码进行编译。默认值：false。
- sourceMap：是否生成 sourceMap。默认值：false。

### 4.2.6 严格检查

- strict：启用所有的严格检查，**默认值为true，设置后相当于开启了所有的严格检查**
- alwaysStrict：总是以严格模式对代码进行编译
- noImplicitAny：禁止隐式的any类型
- noImplicitThis：禁止类型不明确的this
- strictBindCallApply：严格检查 bind、call 和 apply 的参数列表
- strictFunctionTypes：严格检查函数的类型
- strictNullChecks：严格的空值检查
- strictPropertyInitialization：严格检查属性是否初始化

### 4.2.7 额外检查

- noFallthroughCasesInSwitch：检查switch语句包含正确的break
- noImplicitReturns：检查函数没有隐式的返回值
- noUnusedLocals：检查未使用的局部变量
- noUnusedParameters：检查未使用的参数

### 4.2.8 高级

- allowUnreachableCode：检查不可达代码。可选值：
  - true，忽略不可达代码
  - false，不可达代码将引起错误
- noEmitOnError：有错误的情况下不进行编译。默认值：false。

# 五. Webpack

通常情况下，实际开发中我们都需要使用构建工具对代码进行打包，TS 同样也可以结合构建工具一起使用，下边以 webpack 为例介绍一下如何结合构建工具使用 TS。

1. 初始化项目：

进入项目根目录，执行：

```shell
npm init -y
```

2. 安装 Webpack：

```shell
npm i -D webpack webpack-cli webpack-dev-server typescript ts-loader clean-webpack-plugin
```

共**安装了 7 个包**：

- webpack：构建工具webpack
- webpack-cli：webpack的命令行工具
- webpack-dev-server：webpack的开发服务器
- typescript：ts编译器
- ts-loader：ts加载器，用于在webpack中编译ts文件
- html-webpack-plugin：webpack中html插件，用来自动创建html文件
- clean-webpack-plugin：webpack中的清除插件，每次构建都会先清除目录

3. 根目录下创建 webpack 的配置文件 webpack.config.js：

模板：

```js
const path = require("path")
const HtmlWebpackPlugin = require("html-webpack-plugin")
const { CleanWebpackPlugin } = require("clean-webpack-plugin")

module.exports = {
    optimization:{
        minimize: false // 关闭代码压缩，可选
    },

    entry: "./src/index.ts",
    
    devtool: "inline-source-map",
    
    devServer: {
        contentBase: './dist'
    },

    output: {
        path: path.resolve(__dirname, "dist"),
        filename: "bundle.js",
        environment: {
            arrowFunction: false // 关闭webpack的箭头函数，可选
        }
    },

    resolve: {
        extensions: [".ts", ".js"]
    },
    
    module: {
        rules: [
            {
                test: /\.ts$/,
                use: {
                   loader: "ts-loader"     
                },
                exclude: /node_modules/
            }
        ]
    },

    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            title:'TS测试'
        }),
    ]

}
```

根目录下创建 tsconfig.json，配置可以根据自己需要：

```json
{
    "compilerOptions": {
        "target": "ES6",
        "module": "ES6",
        "strict": true
    }
}
```

修改 package.json 添加如下配置：

```json
{
  // ...
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack",
    "start": "webpack serve --open chrome.exe"
  },
  // ...  
}
```

在 src 下创建 ts 文件，并在并命令行执行：

```shell
npm run build
```

 对代码进行编译，或者执行：

```shell
npm start 
```

来启动开发服务器。

# 六. Babel

经过一系列的配置，使得 TS 和 webpack 已经结合到了一起，除了webpack，开发中还经常需要结合 babel 来对代码进行转换以使其可以兼容到更多的浏览器，在上述步骤的基础上，通过以下步骤再将 babel 引入到项目中。

1. 安装依赖包：

```shell
npm i -D @babel/core @babel/preset-env babel-loader core-js
```

共**安装了 4 个包**，分别是：

- @babel/core：babel 的核心工具
- @babel/preset-env：babel 的预定义环境
- @babel-loader：babel 在 webpack 中的加载器
- core-js：core-js 用来使老版本的浏览器支持新版ES语法

2. 修改 webpack.config.js 配置文件：

```js
module: {
    rules: [
        {
            test: /\.ts$/,
            use: [
                {
                    loader: "babel-loader",
                    options:{
                        presets: [
                            [
                                "@babel/preset-env",
                                {
                                    "targets":{
                                        "chrome": "58",
                                        "ie": "11"
                                    },
                                    "corejs":"3",
                                    "useBuiltIns": "usage"
                                }
                            ]
                        ]
                    }
                },
                {
                    loader: "ts-loader",

                }
            ],
            exclude: /node_modules/
        }
    ]
}
```