
# `Inline`

```js
import { Inline } from 'slate'
```

Slate [`Document`](./document.md) Inline 节点实现了 [`Node`](./node.md) 接口。

Block 节点可以包含嵌套的 inline 节点以及 text 节点——就像 DOM 一样。它始终包含至少一个 text 节点作为子节点。

- [Properties](#properties)
  - [`data`](#data)
  - [`isVoid`](#isvoid)
  - [`key`](#key)
  - [`nodes`](#nodes)
  - [`type`](#type)
- [Computed Properties](#computed-properties)
  - [`kind`](#kind)
  - [`text`](#text)
- [Static Methods](#static-methods)
  - [`Inline.create`](#inlinecreate)
  - [`Inline.createList`](#inlinecreatelist)
  - [`Inline.fromJSON`](#inlinefromjson)
  - [`Inline.isInline`](#inlineisinline)
- [Node Methods](#node-methods)
- [Instance Methods](#instance-methods)
  - [`toJSON`](#tojson)


## Properties

```js
Inline({
  data: Data,
  isVoid: Boolean,
  key: String,
  nodes: Immutable.List<Node>,
  type: String
})
```

### `data`
`Immutable.Map`

与节点相关的任意数据。默认为一个空 `Map`。

### `isVoid`
`Boolean`

节点是否为 "void"，这意味着其没有子内容（如 emoji 表情、图标等）。默认为 `false`。

注意即便节点为 "void"，它仍然会包含一个空的 [`Text`](./text.md) 节点以保证适配其它操作的一致性。不过，在 Slate 渲染时这个 [`Text`](./text.md) 节点是不可见的。

### `key`
`String`

节点的唯一标识。

### `nodes`
`Immutable.List`

子节点列表。默认为包含单一文本节点的列表。

### `type`
`String`

节点的自定义类型（如 `link` 或 `hashtag`）。


## Computed Properties

### `kind`
`String`

不可变的 String，值为 `'inline'` 以便于将这类节点与 [`Block`](./block.md) 和 [`Text`](./text.md) 节点区分开。

### `text`
`String`

该节点的全部子 [`Text`](./text.md) 节点连接成的字符串。


## Static Methods

### `Inline.create`
`Inline.create(properties: Object) => Inline`

由原生 JS `properties` 对象创建一个 inline。

### `Inline.createList`
`Inline.createList(array: Array) => List`

由原生 JS `array` 数组创建一个 inline 列表。

### `Inline.fromJSON`
`Inline.fromJSON(object: Object) => Inline`

由 JSON `object` 创建一个 inline。

### `Inline.isInline`
`Inline.isInline(maybeInline: Any) => Boolean`

返回传入的参数是否为 `Inline` 的 boolean 值。


## Node Methods

Inline 实现了 [`Node`](./node.md) 接口。对所有 node 方法的相关信息，参见 [`Node` 文档](./node.md)。


## Instance Methods

### `toJSON`
`toJSON() => Object`

返回 inline 的 JSON 表示。
