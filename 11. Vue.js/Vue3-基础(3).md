# Vue3基础入门3

# 十一. 表单输入绑定

在前端处理表单时，我们常常需要将表单输入框的内容同步给 JavaScript 中相应的变量。手动连接值绑定和更改事件监听器可能会很麻烦：

```vue
<!-- 其实这也是一种单向数据流，而不是双向绑定式的 -->
<input
  :value="text"
  @input="event => text = event.target.value">
```

`v-model` 指令帮我们简化了这一步骤：

```vue
<input v-model="text">
```

另外，`v-model` 还可以用于各种不同类型的输入，`<textarea>`、`<select>` 元素。它会根据所使用的元素自动使用对应的 DOM 属性和事件组合：

- 文本类型的 `<input>` 和 `<textarea>` 元素会绑定 `value` property 并侦听 `input` 事件；
- `<input type="checkbox">` 和 `<input type="radio">` 会绑定 `checked` property 并侦听 `change` 事件；
- `<select>` 会绑定 `value` property 并侦听 `change` 事件：

## 11.1 基本用法

### 11.1.1 文本











### 11.1.2 多行文本









### 11.1.3 复选框









### 11.1.4 单选框











### 11.1.5 选择器







## 11.2 值绑定





















# 十二. 生命周期









# 十三. 侦听器



















# 十四. 模板引用















# 十五. 组件基础













