
# `Mark`

```js
import { Mark } from 'slate'
```

与 [`Characters`](./mark.md) 相结合的格式化标记。Mark 是 Slate 表示如 **加粗** 和 _斜体_ 等富文本格式的方式。

- [Properties](#properties)
  - [`data`](#data)
  - [`type`](#type)
- [Static Methods](#static-methods)
  - [`Mark.create`](#markcreate)
  - [`Mark.createSet`](#markcreateset)
  - [`Mark.fromJSON`](#markfromjson)
  - [`Mark.isMark`](#markismark)
- [Instance Methods](#instance-methods)
  - [`toJSON`](#tojson)


## Properties

```js
Mark({
  data: Data,
  type: String
})
```

### `data`
`Data`

[`Data`](./data.md) 格式的 map。.

### `type`
`String`

mark 的自定义格式（如 `bold` 或 `italic`）。


## Static Methods

### `Mark.create`
`Mark.create(properties: Object) => Mark`

由原生 JS `properties` 对象构造出一个 mark。

### `Mark.createSet`
`Mark.createSet(array: Array) => Set`

由原生 JS `array` 数组构造出一个 character 集合。

### `Mark.fromJSON`
`Mark.fromJSON(object: Object) => Mark`

由 JSON `object` 创建一个 mark。

### `Mark.isMark`
`Mark.isMark(maybeMark: Any) => Boolean`

返回传入参数是否为 `Mark` 的布尔值。


## Instance Methods

### `toJSON`
`toJSON() => Object`

返回 mark 的 JSON 表示。
