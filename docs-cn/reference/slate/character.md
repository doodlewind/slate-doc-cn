
# `Character`

```js
import { Character } from 'slate'
```

[`Text`](./text.md) 节点内的一个字符。

Character 是 Slate 将一段文本与 [`Mark`](./mark.md) 相组合以控制格式的方式。

- [Properties](#properties)
  - [`marks`](#marks)
  - [`text`](#text)
- [Static Methods](#static-methods)
  - [`Character.create`](#charactercreate)
  - [`Character.createList`](#charactercreatelist)
  - [`Character.fromJSON`](#characterfromjson)
  - [`Character.isCharacter`](#characterischaracter)
- [Instance Methods](#instance-methods)
  - [`toJSON`](#tojson)


## Properties

```js
Character({
  marks: Immutable.Set<Mark>,
  text: String
})
```

### `marks`
`Immutable.Set`

应用到 character 上的 [`Mark`](./mark.md) 集合。

### `text`
`String`

Character 对应的文本字符串。


## Static Methods

### `Character.create`
`Character.create(properties: Object) => Character`

由原生 JS `properties` 对象构造出一个 character。

### `Character.createList`
`Character.createList(array: Array) => List`

由原生 JS `array` 数组构造出一个 character 列表。

### `Character.fromJSON`
`Character.fromJSON(object: Object) => Character`

由 JSON `object` 创建一个 character。

### `Character.isCharacter`
`Character.isCharacter(maybeCharacter: Any) => Boolean`

返回传入参数是否为 `Character` 的布尔值。


## Instance Methods

### `toJSON`
`toJSON() => Object`

返回 character 的 JSON 表示。
