
# `Document`

```js
import { Document } from 'slate'
```

Slate 中文档模型的顶层节点。

Document 是由 block 节点、inline 节点和 text 节点组成的——和 DOM 一致。注意 document 节点的直接子节点必须是 block 节点。

在一些地方，你会看到 "fragment" 的概念，这实际上是未挂载到主 `State` 的 `Docuement` 对象。例如，在剪贴选择的内容时，我们认为内容是一个文档 "fragment"。

- [Properties](#properties)
  - [`data`](#data)
  - [`nodes`](#nodes)
- [Computed Properties](#computed-properties)
  - [`kind`](#kind)
  - [`text`](#text)
- [Static Methods](#static-methods)
  - [`Document.create`](#documentcreate)
  - [`Document.fromJSON`](#documentfromjson)
  - [`Document.isDocument`](#documentisdocument)
- [Node Methods](#node-methods)
- [Instance Methods](#instance-methods)
  - [`toJSON`](#tojson)


## Properties

```js
Document({
  nodes: Immutable.List<Node>,
})
```

### `data`
`Immutable.Map`

任意与 document 关联的 data 对象。默认为空 `Map`。

### `nodes`
`Immutable.List`

子节点列表。


## Computed Properties

### `kind`
`String`

不可变的 String，值为 `document` 以便于将这类节点与 [`Block`](./block.md)、[`Inline`](./inline.md) 和 [`Text`](./text.md) 节点区分开。

### `text`
`String`

该节点的全部子 [`Text`](./text.md) 节点连接成的字符串。


## Static Methods

### `Document.create`
`Document.create(properties: Object) => Document`

由原生 JS `properties` 对象创建一个 document。

### `Document.fromJSON`
`Document.fromJSON(object: Object) => Document`

由 JSON `object` 创建一个 document。

### `Document.isDocument`
`Document.isDocument(maybeDocument: Any) => Boolean`

返回传入的参数是否为 `Document` 的 boolean 值。


## Node Methods

Document 实现了 [`Node`](./node.md) 接口。对所有 node 方法的相关信息，参见 [`Node` 文档](./node.md)。


## Instance Methods

### `toJSON`
`toJSON() => Object`

返回 document 的 JSON 表示。
