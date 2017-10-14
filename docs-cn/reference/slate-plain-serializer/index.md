
# `slate-plain-serializer`

```js
import Plain from 'slate-plain-serializer'
```

支持 Slate [`State`](../slate/state.md) 与纯文本字符串之间相互转换的序列化器。

- [Example](#example)
- [Static Methods](#methods)
  - [`Plain.deserialize`](#plaindeserialize)
  - [`Plain.serialize`](#plainserialize)


## Example

```
The Slate editor gives you full control over the logic you can add.\n
In its simplest form, when representing plain text, Slate is a glorified <textarea>. But you can augment it to be much more than that.\n
Check out http://slatejs.org for examples!
```


## Methods

### `Plain.deserialize`
`Plain.deserialize(string: String, [options: Object]) => State`

将纯文本 `string` 反序列化为 [`State`](../slate/state.md)。将通过 `\n` 字符切分出一系列的 block。每个 block 都会获得一个 `line` 类型。

如果你将 `toJSON: true` 作为选项传入，返回值将为 JSON 对象而非 [`State`](../slate/state.md) 对象。

### `Plain.serialize`
`Plain.serialize(state: State) => String`

将 `state` 序列化为纯文本。文档的每个直接子 block 节点都将由 `\n` 字符分隔。
