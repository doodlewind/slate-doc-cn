
# `Text`

```js
import { Text } from 'slate'
```

Slate [`Document`](./document.md) 中的文本节点。文本节点总是文档中最底层的叶子节点，和 DOM 保持一致。

- [Properties](#properties)
  - [`characters`](#characters)
  - [`key`](#key)
- [Computed Properties](#computed-properties)
  - [`kind`](#kind)
  - [`text`](#text)
- [Static Methods](#static-methods)
  - [`Text.create`](#textcreate)
  - [`Text.fromJSON`](#textfromjson)
  - [`Text.isText`](#textistext)
- [Instance Methods](#instance-methods)
  - [`toJSON`](#tojson)

## Properties

```js
Text({
  characters: Immutable.List<Character>,
  key: String
})
```

### `characters`

组成文本节点内容，并与 [`Mark`](./mark.md) 相关联的 [`Characters`](./character.md) 列表。

### `key`
`String`

节点的唯一标识。


## Computed Properties

### `kind`
`String`

不可变的 String，值为 `'text'` 以便于将这类节点与 [`Inline`](./inline.md) 和 [`Block`](./block.md) 节点区分开。

### `text`
`String`

该文本节点内全部字符连接成的字符串。


## Static Methods

### `Text.create`
`Text.create(properties: Object) => Text`

由原生 JS `properties` 对象创建一个 text。

### `Text.fromJSON`
`Text.fromJSON(object: Object) => Text`

由 JSON `object` 创建一个 text。

### `Text.isText`
`Text.isText(maybeText: Any) => Boolean`

返回传入的参数是否为 `Text` 的 boolean 值。


## Instance Methods

### `toJSON`
`toJSON() => Object`

返回 text 的 JSON 表示。
