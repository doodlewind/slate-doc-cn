
# `Block`

```js
import { Block } from 'slate'
```

Slate [`Document`](./document.md) 中的块级节点。Block 节点实现了 [`Node`](./node.md) 接口。

Block 节点可以包含嵌套的 block 节点、inline 节点，以及 text 节点——就像 DOM 一样。它始终包含至少一个 text 节点作为子节点。

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
  - [`Block.create`](#blockcreate)
  - [`Block.createList`](#blockcreatelist)
  - [`Block.fromJSON`](#blockfromjson)
  - [`Block.isBlock`](#blockisblock)
- [Node Methods](#node-methods)
- [Instance Methods](#instance-methods)
  - [`toJSON`](#tojson)


## Properties

```js
Block({
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

节点是否为 "void"，这意味着其没有子内容（如图片、视频等）。默认为 `false`。 

注意即便节点为 "void"，它仍然会包含一个空的 [`Text`](./text.md) 节点以保证适配其它操作的一致性。不过，在 Slate 渲染时这个 [`Text`](./text.md) 节点是不可见的。

### `key`
`String`

节点的唯一标识。

### `nodes`
`Immutable.List`

子节点列表。默认为包含单一文本节点的列表。

### `type`
`String`

节点的自定义类型（如 `blockquote` 或 `list-item`）。


## Computed Properties

### `kind`
`String`

不可变的 String，值为 `'block'` 以便于将这类节点与 [`Inline`](./inline.md) 和 [`Text`](./text.md) 节点区分开。

### `text`
`String`

该节点的全部子 [`Text`](./text.md) 节点连接成的字符串。


## Static Methods

### `Block.create`
`Block.create(properties: Object) => Block`

由原生 JS `properties` 对象创建一个 block。

### `Block.createList`
`Block.createList(array: Array) => List`

由原生 JS `array` 数组创建一个 block 列表。

### `Block.fromJSON`
`Block.fromJSON(object: Object) => Block`

由 JSON `object` 创建一个 block。

### `Block.isBlock`
`Block.isBlock(maybeBlock: Any) => Boolean`

返回传入的参数是否为 `Block` 的 boolean 值。


## Node Methods

Block 实现了 [`Node`](./node.md) 接口。对所有 node 方法的相关信息，参见 [`Node` 文档](./node.md)。


## Instance Methods

### `toJSON`
`toJSON() => Object`

返回 block 的 JSON 表示。
