
# `Selection`

```js
import { Selection } from 'slate'
```

Slate [`Document`](./document.md) 中的选择范围。Slate 中的 Selection 是按照原生 [DOM Selection API](https://developer.mozilla.org/en-US/docs/Web/API/Selection) 设计的，使用形如 "anchor"、"focus" 和 "collapsed" 的术语。

"anchor" 是选择范围中固定不动的端点，而 "focus" 为非固定的端点，在移动光标位置时可能移动（如按下 `Shift + →` 时）。

很多情况下，你不需要知道哪个端点是 "anchor"，哪个端点又是 "focus"，只想知道它们在文档中的前后顺序。这时，通过 "start" 和 "end" 点可以使用很多便捷而等效的属性和方法。

- [Properties](#properties)
  - [`anchorKey`](#anchorkey)
  - [`anchorOffset`](#anchoroffset)
  - [`focusKey`](#focuskey)
  - [`focusOffset`](#focusoffset)
  - [`isBackward`](#isbackward)
  - [`isFocused`](#isfocused)
- [Computed Properties](#computed-properties)
  - [`endKey`](#endkey)
  - [`endOffset`](#endoffset)
  - [`isBlurred`](#isblurred)
  - [`isCollapsed`](#iscollapsed)
  - [`isExpanded`](#isExpanded)
  - [`isForward`](#isForward)
  - [`startKey`](#startkey)
  - [`startOffset`](#startoffset)
- [Static Methods](#static-methods)
  - [`Selection.create`](#selectioncreate)
  - [`Selection.fromJSON`](#selectionfromjson)
  - [`Selection.isSelection`](#selectionisselection)
- [Instance Methods](#instance-methods)
  - [`toJSON`](#tojson)
- [Checking Methods](#checking-methods)
  - [`has{Edge}AtEndOf`](#hasedgeatendof)
  - [`has{Edge}AtStartOf`](#hasedgeatstartof)
  - [`has{Edge}Between`](#hasedgebetween)
  - [`has{Edge}In`](#hasedgein)
  - [`isAtEndOf`](#isatendof)
  - [`isAtStartOf`](#isatstartof)


## Properties

```js
Selection({
  anchorKey: String,
  anchorOffset: Number,
  focusKey: String,
  focusOffset: Number,
  isFocused: Boolean,
  isBackward: Boolean  
})
```

### `anchorKey`
`String`

选择范围锚点所在文本节点的 key。

### `anchorOffset`
`Number`

从选择范围锚点所在文本节点开始位置起的字符数量。

### `focusKey`
`String`

选择范围焦点所在文本节点的 key。

### `focusOffset`
`Number`

从选择范围焦点所在文本节点开始位置起的字符数量。

### `isBackward`
`Boolean`

选择范围是否反向。若选择范围焦点在文档中位置出现于锚点前，则认为其【反向】。

### `isFocused`
`Boolean`

选择范围是否具有焦点。


## Computed Properties

在创建选择范围时并不提供这些属性，它们是通过真实属性计算得出的。

### `isBlurred`
`Boolean`

为方便起见，与 `isFocused` 相反。

### `isCollapsed`
`Boolean`

选择范围是否收缩。当选择范围中锚点与焦点相等时认为其【收缩】。

### `isExpanded`
`Boolean`

为方便起见，与 `isExpanded` 相反。

### `isForward`
`Boolean`

为方便起见，与 `isBackward` 相反。

### `startKey`
### `startOffset`
### `endKey`
### `endOffset`

用于访问选择范围首尾位置的若干便捷属性。当选择范围为前向时，`start` 指向 `anchor` 端点，`end` 指向 `focus` 端点。当选择范围为后向时则相反。


## Static Methods

### `Selection.create`
`Selection.create(properties: Object) => Selection`

使用 `properties` 创建新 `Selection` 实例。

### `Selection.fromJSON`
`Selection.fromJSON(object: Object) => Selection`

由 JSON `object` 创建选择范围。

### `Selection.isSelection`
`Selection.isSelection(maybeSelection: Any) => Boolean`

返回传入参数是否为 `Selection` 的 boolean 值。


## Instance Methods

### `toJSON`
`toJSON() => Object`

返回选择范围的 JSON 表示。


## Checking Methods

### `has{Edge}AtStartOf`
`has{Edge}AtStartOf(node: Node) => Boolean`

判断选择范围是否有位于 `node` 开始位置的边缘。`{Edge}` 可为 `Anchor`、`Focus`、`Start`、`End` 或 `Edge`（对应其中任一端点）。

### `has{Edge}AtEndOf`
`has{Edge}AtEndOf(node: Node) => Boolean`

判断选择范围是否有位于 `node` 结束位置的边缘。`{Edge}` 可为 `Anchor`、`Focus`、`Start`、`End` 或 `Edge`（对应其中任一端点）。

### `has{Edge}Between`
`has{Edge}Between(node: Node, start: Number, end: Number) => Boolean`

判断选择范围是否有位于 `node` 中 `start` 与 `end` 之间位置的边缘。`{Edge}` 可为 `Anchor`、`Focus`、`Start`、`End` 或 `Edge`（对应其中任一端点）。

### `has{Edge}In`
`has{Edge}In(node: Node) => Boolean`

判断选择范围是否有位于 `node` 内位置的边缘。`{Edge}` 可为 `Anchor`、`Focus`、`Start`、`End` 或 `Edge`（对应其中任一端点）。

### `isAtStartOf`
`isAtStartOf(node: Node) => Boolean`

判断选择范围是否位于 `node` 开始位置。

### `isAtEndOf`
`isAtEndOf(node: Node) => Boolean`

判断选择范围是否位于 `node` 结束位置。
