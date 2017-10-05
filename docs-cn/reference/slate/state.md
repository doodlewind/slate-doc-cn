
# `State`

```js
import { State } from 'slate'
```

`State` 中包含 [`Document`](./document.md) 和 [`Selection`](./selection.md)，是 Slate 中对数据的顶层表示方式。你需要将它传入 Slate [`<Editor>`](../slate-react/editor.md) 中来渲染页面。

所有对文档和选中范围的变更都是通过 state 对象实现的，这样它们能够保持同步，并衍生出其内部的撤销 / 重做历史。

为方便起见，除了 change 外，许多 [`Selection`](./selection.md) 与 [`Document`](./document.md) 的属性也通过代理的形式在 `State` 对象上开放。

- [Properties](#properties)
  - [`document`](#document)
  - [`selection`](#selection)
- [Computed Properties](#computed-properties)
  - [`{edge}Text`](#edgetext)
  - [`{edge}Block`](#edgeblock)
  - [`marks`](#marks)
  - [`activeMarks`](#activeMarks)
  - [`blocks`](#blocks)
  - [`fragment`](#fragment)
  - [`inlines`](#inlines)
  - [`texts`](#texts)
  - [`hasUndos`](#hasundos)
  - [`hasRedos`](#hasredos)
- [Selection-like Properties](#selection-like-properties)
  - [`{edge}Key`](#edgekey)
  - [`{edge}Offset`](#edgeoffset)
  - [`isBackward`](#isbackward)
  - [`isBlurred`](#isblurred)
  - [`isCollapsed`](#iscollapsed)
  - [`isExpanded`](#isExpanded)
  - [`isFocused`](#isfocused)
  - [`isForward`](#isForward)
  - [`isEmpty`](#isEmpty)
- [Static Methods](#static-methods)
  - [`State.create`](#statecreate)
  - [`State.fromJSON`](#statefromjson)
  - [`State.isState`](#stateisstate)
- [Instance Methods](#instance-methods)
  - [`change`](#change)
  - [`toJSON`](#tojson)


## Properties

```js
State({
  document: Document,
  selection: Selection
})
```

### `document`
`Document`

当前 state 对应的文档。

### `selection`
`Selection`

当前 state 对应的选择范围。


## Computed Properties

这些属性不需在创建 `State` 时提供，而是通过当前 `document` 与 `selection` 计算得出。

### `{edge}Text`
`Text`

获得 `{edge}` 位置的叶子 [`Text`](./text.md) 节点。`{edge}` 可为：`anchor`、`focus`、`start` 或 `end`。


### `{edge}Block`
`Block`

获得 `{edge}` 位置的叶子 [`Block`](./block.md) 节点。`{edge}` 可为：`anchor`、`focus`、`start` 或 `end`。

### `marks`
`Set`

获得当前选择范围中的 [`Marks`](./mark.md) 集合。

### `activeMarks`
`Set`

获得当前选择范围中 _全部_ 字符对应的 [`Marks`](./mark.md) 子集。这可以用来在常见富文本编辑器习惯的基础上，根据 mark 来判断工具栏按钮的激活 / 非激活状态。

### `blocks`
`List`

获得当前选择范围中最底层 [`Block`](./block.md) 节点的列表。

### `fragment`
`Document`

获得当前选择范围的 [`Document`](./document.md) fragment 表示。

### `inlines`
`List`

获得当前选择范围中最底层 [`Inline`](./inline.md) 节点的列表。

### `texts`
`List`

获得当前选择范围中全部 [`Text`](./text.md) 节点的列表。

### `characters`
`List`

获得当前选择范围中全部 [`Character`](./character.md) 对象的列表。

### `hasUndos`
`Boolean`

历史记录中是否存在可撤销的快照。

### `hasRedos`
`Boolean`

历史记录中是否存在可重做的快照。

## Selection-like Properties

这些属性是与 [`Selection`](./selection) 相等价的准确代理。

### `{edge}Key`
`String`

获得 `{edge}` 中的当前 key。`{edge}` 可为：`anchor`、`focus`、`start` 或 `end`。

### `{edge}Offset`
`Number`

获得 `{edge}` 中的当前偏移量。`{edge}` 可为：`anchor`、`focus`、`start` 或 `end`。

### `isBackward`
`Boolean`

当前选择范围是否反向。

### `isBlurred`
`Boolean`

当前选择范围是否失去焦点。

### `isCollapsed`
`Boolean`

当前选择范围是否收缩。

### `isExpanded`
`Boolean`

当前选择范围是否扩展。

### `isFocused`
`Boolean`

当前选择范围是否获得焦点。

### `isForward`
`Boolean`

当前选择范围是否前向。

### `isEmpty`
`Boolean`

当前选择范围是否为空。

## Static Methods

### `State.create`
`State.create(properties: Object) => State`

由 `properties` 对象创建一个新 `State` 实例。

### `State.fromJSON`
`State.fromJSON(object: Object) => State`

由 JSON `object` 创建一个 state。

### `State.isState`
`State.isState(maybeState: Any) => Boolean`

返回传入参数是否为 `State` 的 boolean 值。


## Instance Methods

### `change`
`change() => Change`

创建一个作用于当前 state 的新 [`Change`](./change.md)。

### `toJSON`
`toJSON() => Object`

返回 state 的 JSON 表示。
