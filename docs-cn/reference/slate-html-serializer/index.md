
# `slate-html-serializer`

```js
import Html from 'slate-html-serializer'
```

你可以使用 HTML 序列化器来根据你的 schema 使用场景，解析并序列化任意的 HTML 内容。你需要传入一系列的 `rules` 来定义 Slate schema 如何与 HTML 互相转换。

对可用的 HTML 序列化器例子，可参见 [`paset-html` 示例](http://slatejs.org/#/paste-html)。

- [Example](#example)
- [Properties](#properties)
  - [`rules`](#rules)
  - [`defaultBlock`](#defaultblock)
  - [`parseHtml`](#parsehtml)
- [Methods](#methods)
  - [`deserialize`](#deserialize)
  - [`serialize`](#serialize)
- [Rules](#rules)
  - [`rule.deserialize`](#ruledeserialize)
  - [`rule.serialize`](#ruleserialize)


## Example

```
<p>The Slate editor gives you <em>complete</em> control over the logic you can add.</p>
<p>In its simplest form, when representing plain text, Slate is a glorified <code>&laquo;textarea&raquo;</code>. But you can augment it to be much more than that.</p>
<p>Check out <a href="http://slatejs.org">http://slatejs.org</a> for examples!</p>
```


## Properties

```js
new Html({
  rules: Array
})
```

### `rules`
`Array`

初始化 HTML 序列化器的规则数组，用于定义 schema。

### `defaultBlock`
`String|Object|Block`

供未匹配到规则的 block 使用的属性集合。可为形如 `'paragraph'` 的字符串、形如 `{ type: 'paragraph' }` 这样带有 `type` 属性的对象，甚至一个 [`Block`](../slate/block.md)。

### `parseHtml`
`Function`

用于解析 HTML 字符串并返回 DOM 对象的函数。默认在支持的浏览器环境下使用原生的 `DOMParser`。对较旧的浏览器或服务端渲染的场景，你可以导入 [parse5](https://www.npmjs.com/package/parse5) 包并将 `parse5.parseFragment` 作为 `parseHtml` 选项提供。

## Methods

### `Html.deserialize`
`Html.deserialize(html: String, [options: Object]) => State`

反序列号 HTML `string` 为 [`State`](../slate/state.md)。字符串的反序列化方式取决于构造 HTML 序列化器的规则。

如果你将 `toJSON: true` 作为选项传入，返回值将为 JSON 对象而非 [`State`](../slate/state.md) 对象。

### `Html.serialize`
`Html.serialize(state: State, [options: Object]) => String || Array`

将 `state` 序列化为 HTML 字符串。字符串的反序列化方式取决于构造 HTML 序列化器的规则。

如果你将 `render: false` 作为选项传入，返回值将替换为顶层 React 元素的 iterable 列表，以将其作为子节点在你的 React 组件中渲染。


## Rules

要初始化一个 HTML 序列化器，你需要传入一个规则数组来定义 schema。每条规则通过实现两个函数接口的形式，来定义如何反序列化与序列化 node 或 mark。

每条规则必须定义两个属性：

```js
{
  deserialize: Function,
  serialize: Function
}
```


#### `rule.deserialize`
`rule.deserialize(el: Element, next: Function) => Object || Void`

`deserialize` 函数接收一个 DOM 元素并返回表示反序列化后状态的纯 JS 对象，并在规则不能确定如何反序列化对象时返回空值，此时将尝试规则栈中的下一条规则。

对象需为下列格式其中之一：

```js
{
  kind: 'block',
  type: String,
  data: Object,
  nodes: next(...)
}

{
  kind: 'inline',
  type: String,
  data: Object,
  nodes: next(...)
}

{
  kind: 'mark',
  type: String,
  data: Object,
  nodes: next(...)
}

{
  kind: 'text',
  ranges: Array
}
```


#### `rule.serialize`
`rule.serialize(object: Node || Mark || String, children: String || Element || Array) => Element || Void`

`serialize` 函数返回代表序列化后 HTML 的 React 元素，并在规则不能确定如何序列化对象时返回空值，此时将尝试规则栈中的下一条规则。

该函数调用时将传入以下三者其中之一：`Node`、`Mark`、带 `kind: 'string'` 属性与文本 `text` 属性的特殊 `String` 不可变对象。
