# 开始

## 总览

Vite（法语意为 "快速的"，发音 `/vit/`，发音同 "veet"）是一种新型前端构建工具，能够显著提升前端开发体验。它主要由两部分组成：

- 一个开发服务器，它基于 [原生 ES 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) 提供了 [丰富的内建功能](https://cn.vitejs.dev/guide/features.html)，如速度快到惊人的 [模块热更新（HMR）](https://cn.vitejs.dev/guide/features.html#hot-module-replacement)。
- 一套构建指令，它使用 [Rollup](https://rollupjs.org/) 打包你的代码，并且它是预配置的，可输出用于生产环境的高度优化过的静态资源。

Vite 意在提供开箱即用的配置，同时它的 [插件 API](https://cn.vitejs.dev/guide/api-plugin.html) 和 [JavaScript API](https://cn.vitejs.dev/guide/api-javascript.html) 带来了高度的可扩展性，并有完整的类型支持。

你可以在 [为什么选 Vite](https://cn.vitejs.dev/guide/why.html) 中了解更多关于项目的设计初衷。

## 浏览器支持

默认的构建目标是能支持 [原生 ESM 语法的 script 标签](https://caniuse.com/es6-module)、[原生 ESM 动态导入](https://caniuse.com/es6-module-dynamic-import) 和 [`import.meta`](https://caniuse.com/mdn-javascript_operators_import_meta) 的浏览器。传统浏览器可以通过官方插件 [@vitejs/plugin-legacy](https://github.com/vitejs/vite/tree/main/packages/plugin-legacy) 支持 —— 查看 [构建生产版本](https://cn.vitejs.dev/guide/build.html) 章节获取更多细节。

## 搭建第一个 Vite 项目

Vite 需要 [Node.js](https://nodejs.org/en/) 版本 14.18+，16+。然而，有些模板需要依赖更高的 Node 版本才能正常运行，当你的包管理器发出警告时，请注意升级你的 Node 版本。

使用 NPM：

```shell
npm create vite@latest
```

使用 Yarn：

```shell
yarn create vite
```

使用 PNPM：

```shell
pnpm create vite
```

然后按照提示操作即可！你还可以通过附加的命令行选项直接指定项目名称和你想要使用的模板。例如，要构建一个 Vite + Vue 项目，运行：

```shell
# npm 6.x
npm create vite@latest my-vue-app --template vue

# npm 7+, extra double-dash is needed:
npm create vite@latest my-vue-app -- --template vue

# yarn
yarn create vite my-vue-app --template vue

# pnpm
pnpm create vite my-vue-app --template vue
```

## `index.html` 与项目根目录

你可能已经注意到，在一个 Vite 项目中，`index.html` 在项目最外层而不是在 `public` 文件夹内。这是有意而为之的：在开发期间 Vite 是一个服务器，而 `index.html` 是该 Vite 项目的入口文件。

Vite 将 `index.html` 视为源码和模块图的一部分。Vite 解析 `<script type="module" src="...">` ，这个标签指向你的 JavaScript 源码。甚至内联引入 JavaScript 的 `<script type="module">` 和引用 CSS 的 `<link href>` 也能利用 Vite 特有的功能被解析。另外，`index.html` 中的 URL 将被自动转换，因此不再需要 `%PUBLIC_URL%` 占位符了。

与静态 HTTP 服务器类似，Vite 也有 “根目录” 的概念，即服务文件的位置，在接下来的文档中你将看到它会以 `<root>` 代称。源码中的绝对 URL 路径将以项目的 “根” 作为基础来解析，因此你可以像在普通的静态文件服务器上一样编写代码（并且功能更强大！）。Vite 还能够处理依赖关系，解析处于根目录外的文件位置，这使得它即使在基于 monorepo 的方案中也十分有用。

Vite 也支持多个 `.html` 作入口点的 [多页面应用模式](https://cn.vitejs.dev/guide/build.html#multi-page-app)。

#### 指定替代根目录

`vite` 以当前工作目录作为根目录启动开发服务器。你也可以通过 `vite serve some/sub/dir` 来指定一个替代的根目录。

## 命令行界面

在安装了 Vite 的项目中，可以在 npm scripts 中使用 `vite` 可执行文件，或者直接使用 `npx vite` 运行它。下面是通过脚手架创建的 Vite 项目中默认的 npm scripts：

```json
{
  "scripts": {
    "dev": "vite", // 启动开发服务器，别名：`vite dev`，`vite serve`
    "build": "vite build", // 为生产环境构建产物
    "preview": "vite preview" // 本地预览生产构建产物
  }
}
```

可以指定额外的命令行选项，如 `--port` 或 `--https`。运行 `npx vite --help` 获得完整的命令行选项列表。

# 功能

对非常基础的使用来说，使用 Vite 开发和使用一个静态文件服务器并没有太大区别。然而，Vite 还通过原生 ESM 导入提供了许多主要用于打包场景的增强功能。

## NPM 依赖解析和预构建

原生 ES 导入不支持下面这样的裸模块导入：

```js
import { someMethod } from 'my-dep'
```

上面的代码会在浏览器中抛出一个错误。Vite 将会检测到所有被加载的源文件中的此类裸模块导入，并执行以下操作:

1. [预构建](https://cn.vitejs.dev/guide/dep-pre-bundling.html) 它们可以提高页面加载速度，并将 CommonJS / UMD 转换为 ESM 格式。预构建这一步由 [esbuild](http://esbuild.github.io/) 执行，这使得 Vite 的冷启动时间比任何基于 JavaScript 的打包器都要快得多。
2. 重写导入为合法的 URL，例如 `/node_modules/.vite/deps/my-dep.js?v=f3sf2ebd` 以便浏览器能够正确导入它们。

**依赖是强缓存的**

Vite 通过 HTTP 头来缓存请求得到的依赖，所以如果你想要编辑或调试一个依赖，请按照 [这里](https://cn.vitejs.dev/guide/dep-pre-bundling.html#浏览器缓存) 的步骤操作。

## 模块热替

Vite 提供了一套原生 ESM 的 [HMR API](https://cn.vitejs.dev/guide/api-hmr.html)。 具有 HMR 功能的框架可以利用该 API 提供即时、准确的更新，而无需重新加载页面或清除应用程序状态。Vite 内置了 HMR 到 [Vue 单文件组件（SFC）](https://github.com/vitejs/vite/tree/main/packages/plugin-vue) 和 [React Fast Refresh](https://github.com/vitejs/vite/tree/main/packages/plugin-react) 中。也通过 [@prefresh/vite](https://github.com/JoviDeCroock/prefresh/tree/main/packages/vite) 对 Preact 实现了官方集成。

注意，你不需要手动设置这些 —— 当你通过 [`create-vite`](https://cn.vitejs.dev/guide/) 创建应用程序时，所选模板已经为你预先配置了这些。

## TypeScript

Vite 天然支持引入 `.ts` 文件。

Vite 仅执行 `.ts` 文件的转译工作，并 **不** 执行任何类型检查。并假设类型检查已经被你的 IDE 或构建过程接管了（你可以在构建脚本中运行 `tsc --noEmit` 或者安装 `vue-tsc` 然后运行 `vue-tsc --noEmit` 来对你的 `*.vue` 文件做类型检查）。

Vite 使用 [esbuild](https://github.com/evanw/esbuild) 将 TypeScript 转译到 JavaScript，约是 `tsc` 速度的 20~30 倍，同时 HMR 更新反映到浏览器的时间小于 50ms。

使用 [仅含类型的导入和导出](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html#type-only-imports-and-export) 形式的语法可以避免潜在的 “仅含类型的导入被不正确打包” 的问题，写法示例如下：

```typescript
import type { T } from 'only/types'
export type { T }
```

### TypeScript 编译器选项

`tsconfig.json` 中 `compilerOptions` 下的一些配置项需要特别注意。

#### `isolatedModules`

应该设置为 `true`。

这是因为 `esbuild` 只执行没有类型信息的转译，它并不支持某些特性，如 `const enum` 和隐式类型导入。

你必须在 `tsconfig.json` 中的 `compilerOptions` 下设置 `"isolatedModules": true`。如此做，TS 会警告你不要使用隔离（isolated）转译的功能。

然而，一些库（如：[`vue`](https://github.com/vuejs/core/issues/1228)）不能很好地与 `"isolatedModules": true` 共同工作。你可以在上游仓库修复好之前暂时使用 `"skipLibCheck": true` 来缓解这个错误。

#### `useDefineForClassFields`

从 Vite v2.5.0 开始，如果 TypeScript 的 target 是 `ES2022` 或更高，包括 `ESNext`，则此选项默认值为 `true`。这与 [`tsc` v4.3.2 及以后版本的行为](https://github.com/microsoft/TypeScript/pull/42663) 一致。这也是标准的 ECMAScript 的运行时行为。

但对于那些习惯其他编程语言或旧版本 TypeScript 的开发者来说，这可能是违反直觉的。 你可以参阅 [TypeScript 3.7 发布日志](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#the-usedefineforclassfields-flag-and-the-declare-property-modifier) 中了解更多关于如何兼容的信息。

如果你正在使用一个严重依赖 class fields 的库，请注意该库对此选项的预期设置。

大多数库都希望 `"useDefineForClassFields": true`，如 [MobX](https://mobx.js.org/installation.html#use-spec-compliant-transpilation-for-class-properties)，[Vue Class Components 8.x](https://github.com/vuejs/vue-class-component/issues/465) 等。

但是有几个库还没有兼容这个新的默认值，其中包括 [`lit-element`](https://github.com/lit/lit-element/issues/1030)。如果遇到这种情况，请将 `useDefineForClassFields` 设置为 `false`。

#### 影响构建结果的其他编译器选项

- [`extends`](https://www.typescriptlang.org/tsconfig#extends)
- [`alwaysStrict`](https://www.typescriptlang.org/tsconfig#alwaysStrict)
- [`importsNotUsedAsValues`](https://www.typescriptlang.org/tsconfig#importsNotUsedAsValues)
- [`jsx`](https://www.typescriptlang.org/tsconfig#jsx)
- [`jsxFactory`](https://www.typescriptlang.org/tsconfig#jsxFactory)
- [`jsxFragmentFactory`](https://www.typescriptlang.org/tsconfig#jsxFragmentFactory)
- [`jsxImportSource`](https://www.typescriptlang.org/tsconfig#jsxImportSource)
- [`preserveValueImports`](https://www.typescriptlang.org/tsconfig#preserveValueImports)

如果你的代码库很难迁移到 `"isolatedModules": true`，或许你可以尝试通过第三方插件来解决，比如 [rollup-plugin-friendly-type-imports](https://www.npmjs.com/package/rollup-plugin-friendly-type-imports)。但是，这种方式不被 Vite 官方支持。

### 客户端类型

Vite 默认的类型定义是写给它的 Node.js API 的。要将其补充到一个 Vite 应用的客户端代码环境中，请添加一个 `d.ts` 声明文件：

```
/// <reference types="vite/client" />
```

同时，你也可以将 `vite/client` 添加到 `tsconfig` 中的 `compilerOptions.types` 下：

```
{
  "compilerOptions": {
    "types": ["vite/client"]
  }
}
```

这将会提供以下类型定义补充：

- 资源导入 (例如：导入一个 `.svg` 文件)
- `import.meta.env` 上 Vite 注入的环境变量的类型定义
- `import.meta.hot` 上的 [HMR API](https://cn.vitejs.dev/guide/api-hmr.html) 类型定义

## Vue

Vite 为 Vue 提供第一优先级支持：

- Vue 3 单文件组件支持：[@vitejs/plugin-vue](https://github.com/vitejs/vite/tree/main/packages/plugin-vue)
- Vue 3 JSX 支持：[@vitejs/plugin-vue-jsx](https://github.com/vitejs/vite/tree/main/packages/plugin-vue-jsx)
- Vue 2.7 支持：[@vitejs/vite-plugin-vue2](https://github.com/vitejs/vite-plugin-vue2)
- Vue <2.7 的支持：[underfin/vite-plugin-vue2](https://github.com/underfin/vite-plugin-vue2)

## JSX

`.jsx` 和 `.tsx` 文件同样开箱即用。JSX 的转译同样是通过 [esbuild](https://esbuild.github.io/)。

Vue 用户应使用官方提供的 [@vitejs/plugin-vue-jsx](https://github.com/vitejs/vite/tree/main/packages/plugin-vue-jsx) 插件，它提供了 Vue 3 特性的支持，包括 HMR，全局组件解析，指令和插槽。

如果不是在 React 或 Vue 中使用 JSX，自定义的 `jsxFactory` 和 `jsxFragment` 可以使用 [`esbuild` 选项](https://cn.vitejs.dev/config/shared-options.html#esbuild) 进行配置。例如对 Preact：

```js
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  esbuild: {
    jsxFactory: 'h',
    jsxFragment: 'Fragment'
  }
})
```

你可以使用 `jsxInject`（这是一个仅在 Vite 中使用的选项）为 JSX 注入 helper，以避免手动导入：

```js
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  esbuild: {
    jsxInject: `import React from 'react'`
  }
})
```

## CSS

导入 `.css` 文件将会把内容插入到 `<style>` 标签中，同时也带有 HMR 支持。也能够以字符串的形式检索处理后的、作为其模块默认导出的 CSS。

### `@import` 内联和变基

Vite 通过 `postcss-import` 预配置支持了 CSS `@import` 内联，Vite 的路径别名也遵从 CSS `@import`。换句话说，所有 CSS `url()` 引用，即使导入的文件在不同的目录中，也总是自动变基，以确保正确性。

Sass 和 Less 文件也支持 `@import` 别名和 URL 变基（具体请参阅 [CSS Pre-processors](https://cn.vitejs.dev/guide/features.html#css-pre-processors)）。

### PostCSS

如果项目包含有效的 PostCSS 配置 (任何受 [postcss-load-config](https://github.com/postcss/postcss-load-config) 支持的格式，例如 `postcss.config.js`)，它将会自动应用于所有已导入的 CSS。

### CSS Modules

任何以 `.module.css` 为后缀名的 CSS 文件都被认为是一个 [CSS modules 文件](https://github.com/css-modules/css-modules)。导入这样的文件会返回一个相应的模块对象：

```css
/* example.module.css */
.red {
  color: red;
}
```

```js
import classes from './example.module.css'
document.getElementById('foo').className = classes.red
```

CSS modules 行为可以通过 [`css.modules` 选项](https://cn.vitejs.dev/config/shared-options.html#css-modules) 进行配置。

如果 `css.modules.localsConvention` 设置开启了 camelCase 格式变量名转换（例如 `localsConvention: 'camelCaseOnly'`），你还可以使用按名导入。

```js
// .apply-color -> applyColor
import { applyColor } from './example.module.css'
document.getElementById('foo').className = applyColor
```

### CSS 预处理器

由于 Vite 的目标仅为现代浏览器，因此建议使用原生 CSS 变量和实现 CSSWG 草案的 PostCSS 插件（例如 [postcss-nesting](https://github.com/csstools/postcss-plugins/tree/main/plugins/postcss-nesting)）来编写简单的、符合未来标准的 CSS。

话虽如此，但 Vite 也同时提供了对 `.scss`, `.sass`, `.less`, `.styl` 和 `.stylus` 文件的内置支持。没有必要为它们安装特定的 Vite 插件，但必须安装相应的预处理器依赖：

```shell
# .scss and .sass
npm add -D sass

# .less
npm add -D less

# .styl and .stylus
npm add -D stylus
```

如果使用的是单文件组件，可以通过 `<style lang="sass">`（或其他预处理器）自动开启。

Vite 为 Sass 和 Less 改进了 `@import` 解析，以保证 Vite 别名也能被使用。另外，`url()` 中的相对路径引用的，与根文件不同目录中的 Sass/Less 文件会自动变基以保证正确性。

由于 Stylus API 限制，`@import` 别名和 URL 变基不支持 Stylus。

你还可以通过在文件扩展名前加上 `.module` 来结合使用 CSS modules 和预处理器，例如 `style.module.scss`。

### 禁用 CSS 注入页面

自动注入 CSS 内容的行为可以通过 `?inline` 参数来关闭。在关闭时，被处理过的 CSS 字符串将会作为该模块的默认导出，但样式并没有被注入到页面中。

```js
import styles from './foo.css' // 样式将会注入页面
import otherStyles from './bar.css?inline' // 样式不会注入页面
```

## 静态资源处理

导入一个静态资源会返回解析后的 URL：

```js
import imgUrl from './img.png'
document.getElementById('hero-img').src = imgUrl
```

添加一些特殊的查询参数可以更改资源被引入的方式：

```js
// 显式加载资源为一个 URL
import assetAsURL from './asset.js?url'
// 以字符串形式加载资源
import assetAsString from './shader.glsl?raw'
// 加载为 Web Worker
import Worker from './worker.js?worker'
// 在构建时 Web Worker 内联为 base64 字符串
import InlineWorker from './worker.js?worker&inline'
```

## JSON

JSON 可以被直接导入 —— 同样支持具名导入：

```js
// 导入整个对象
import json from './example.json'
// 对一个根字段使用具名导入 —— 有效帮助 treeshaking！
import { field } from './example.json'
```

## Glob 导入

Vite 支持使用特殊的 `import.meta.glob` 函数从文件系统导入多个模块：

```js
const modules = import.meta.glob('./dir/*.js')
```

以上将会被转译为下面的样子：

```js
// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js'),
  './dir/bar.js': () => import('./dir/bar.js')
}
```

你可以遍历 `modules` 对象的 key 值来访问相应的模块：

```js
for (const path in modules) {
  modules[path]().then((mod) => {
    console.log(path, mod)
  })
}
```

匹配到的文件默认是懒加载的，通过动态导入实现，并会在构建时分离为独立的 chunk。如果你倾向于直接引入所有的模块（例如依赖于这些模块中的副作用首先被应用），你可以传入 `{ eager: true }` 作为第二个参数：

```js
const modules = import.meta.glob('./dir/*.js', { eager: true })
```

以上会被转译为下面的样子：

```js
// vite 生成的代码
import * as __glob__0_0 from './dir/foo.js'
import * as __glob__0_1 from './dir/bar.js'
const modules = {
  './dir/foo.js': __glob__0_0,
  './dir/bar.js': __glob__0_1
}
```

### Glob 导入形式

`import.meta.glob` 都支持以字符串形式导入文件，类似于 [以字符串形式导入资源](https://vitejs.dev/guide/assets.html#importing-asset-as-string)。在这里，我们使用了 [Import Reflection](https://github.com/tc39/proposal-import-reflection) 语法对导入进行断言：

```js
const modules = import.meta.glob('./dir/*.js', { as: 'raw' })
```

上面的代码会被转换为下面这样：

```js
// code produced by vite（代码由 vite 输出）
const modules = {
  './dir/foo.js': 'export default "foo"\n',
  './dir/bar.js': 'export default "bar"\n'
}
```

`{ as: 'url' }` 还支持将资源作为 URL 加载。

### 多个匹配模式

第一个参数可以是一个 glob 数组，例如：

```js
const modules = import.meta.glob(['./dir/*.js', './another/*.js'])
```

### 反面匹配模式

同样也支持反面 glob 匹配模式（以 `!` 作为前缀）。若要忽略结果中的一些文件，你可以添加“排除匹配模式”作为第一个参数：

```js
const modules = import.meta.glob(['./dir/*.js', '!**/bar.js'])
```

```js
// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js')
}
```

#### 具名导入

也可能你只想要导入模块中的部分内容，那么可以利用 `import` 选项。

```typescript
const modules = import.meta.glob('./dir/*.js', { import: 'setup' })
```

```typescript
// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js').then((m) => m.setup),
  './dir/bar.js': () => import('./dir/bar.js').then((m) => m.setup)
}
```

当与 `eager` 一同存在时，甚至可以对这些模块进行 tree-shaking。

```typescript
const modules = import.meta.glob('./dir/*.js', { import: 'setup', eager: true })
```

```typescript
// vite 生成的代码
import { setup as __glob__0_0 } from './dir/foo.js'
import { setup as __glob__0_1 } from './dir/bar.js'
const modules = {
  './dir/foo.js': __glob__0_0,
  './dir/bar.js': __glob__0_1
}
```

设置 `import` 为 `default` 可以加载默认导出。

```typescript
const modules = import.meta.glob('./dir/*.js', {
  import: 'default',
  eager: true
})
```

```typescript
// vite 生成的代码
import __glob__0_0 from './dir/foo.js'
import __glob__0_1 from './dir/bar.js'
const modules = {
  './dir/foo.js': __glob__0_0,
  './dir/bar.js': __glob__0_1
}
```

#### 自定义查询[¶](https://cn.vitejs.dev/guide/features.html#custom-queries)

你也可以使用 `query` 选项来提供对导入的自定义查询，以供其他插件使用。

```typescript
const modules = import.meta.glob('./dir/*.js', {
  query: { foo: 'bar', bar: true }
})
```

```typescript
// vite 生成的代码
const modules = {
  './dir/foo.js': () =>
    import('./dir/foo.js?foo=bar&bar=true').then((m) => m.setup),
  './dir/bar.js': () =>
    import('./dir/bar.js?foo=bar&bar=true').then((m) => m.setup)
}
```

### Glob 导入注意事项

请注意：

- 这只是一个 Vite 独有的功能而不是一个 Web 或 ES 标准
- 该 Glob 模式会被当成导入标识符：必须是相对路径（以 `./` 开头）或绝对路径（以 `/` 开头，相对于项目根目录解析）或一个别名路径（请看 [`resolve.alias` 选项](https://cn.vitejs.dev/config/shared-options.html#resolve-alias))。
- Glob 匹配是使用 [fast-glob](https://github.com/mrmlnc/fast-glob) 来实现的 —— 阅读它的文档来查阅 [支持的 Glob 模式](https://github.com/mrmlnc/fast-glob#pattern-syntax)。
- 你还需注意，所有 `import.meta.glob` 的参数都必须以字面量传入。你 **不** 可以在其中使用变量或表达式。

## 动态导入

和 [glob 导入](https://cn.vitejs.dev/guide/features.html#glob-import) 类似，Vite 也支持带变量的动态导入。

```typescript
const module = await import(`./dir/${file}.js`)
```

注意变量仅代表一层深的文件名。如果 `file` 是 `foo/bar`，导入将会失败。对于更进阶的使用详情，你可以使用 [glob 导入](https://cn.vitejs.dev/guide/features.html#glob-import) 功能。

## WebAssembly

预编译的 `.wasm` 文件可以通过 `?init` 来导入。默认导出一个初始化函数，返回值为所导出 wasm 实例对象的 Promise：

```typescript
import init from './example.wasm?init'

init().then((instance) => {
  instance.exports.test()
})
```

`init` 函数还可以将传递给 `WebAssembly.instantiate` 的导入对象作为其第二个参数：

```js
init({
  imports: {
    someFunc: () => {
      /* ... */
    }
  }
}).then(() => {
  /* ... */
})
```

在生产构建当中，体积小于 `assetInlineLimit` 的 `.wasm` 文件将会被内联为 base64 字符串。否则，它们将作为资源复制到 `dist` 目录中，并按需获取。



