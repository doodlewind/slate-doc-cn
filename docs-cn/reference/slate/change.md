
# `Change`

```js
import { Change } from 'slate'
```

通过 Change，你可以定义一系列对当前 [`State`](./state.md) 中 [`Document`](./document.md) 或 [`Selection`](./selection.md) 的变换。

所有的变换都是都通过 `Change` 对象实现的，因而变换的历史能够在撤销、重做操作中保存，并使协同编辑成为可能。

变换可以操作 [`Document`](./document.md) 、[`Selection`](./selection.md)，或同时操作二者。

- [Properties](#properties)
  - [`state`](#state)
- [Methods](#methods)
  - [`apply`](#apply)
  - [`call`](#call)
- [Current State Changes](#current-state-changes)
  - [`deleteBackward`](#deletebackward)
  - [`deleteForward`](#deleteforward)
  - [`delete`](#delete)
  - [`insertBlock`](#insertblock)
  - [`insertFragment`](#insertfragment)
  - [`insertInline`](#insertinline)
  - [`insertText`](#inserttext)
  - [`addMark`](#addmark)
  - [`setBlock`](#setblock)
  - [`setInline`](#setinline)
  - [`splitBlock`](#splitblock)
  - [`splitInline`](#splitinline)
  - [`removeMark`](#removemark)
  - [`toggleMark`](#togglemark)
  - [`unwrapBlock`](#unwrapblock)
  - [`unwrapInline`](#unwrapinline)
  - [`wrapBlock`](#wrapblock)
  - [`wrapInline`](#wrapinline)
  - [`wrapText`](#wraptext)
- [Selection Changes](#selection-changes)
  - [`blur`](#blur)
  - [`collapseTo{Edge}Of`](#collapsetoedgeof)
  - [`collapseTo{Edge}Of{Direction}Block`](#collapsetoedgeofdirectionblock)
  - [`collapseTo{Edge}Of{Direction}Text`](#collapsetoedgeofdirectiontext)
  - [`collapseTo{Edge}`](#collapsetoedge)
  - [`extendTo{Edge}Of`](#extendtoedgeof)
  - [`extend`](#extend)
  - [`flip`](#flip)
  - [`focus`](#focus)
  - [`move`](#move)
  - [`move{Edge}`](#moveedge)
  - [`moveOffsetsTo`](#moveoffsetsto)
  - [`moveToRangeOf`](#movetorangeof)
  - [`select`](#select)
  - [`selectAll`](#selectall)
  - [`deselect`](#deselect)
- [Node Changes](#node-changes)
  - [`addMarkByKey`](#addmarkbykey)
  - [`insertNodeByKey`](#insertnodebykey)
  - [`insertFragmentByKey`](#insertfragmentbykey)
  - [`insertTextByKey`](#inserttextbykey)
  - [`moveNodeByKey`](#movenodebykey)
  - [`removeMarkByKey`](#removemarkbykey)
  - [`removeNodeByKey`](#removenodebykey)
  - [`removeTextByKey`](#removetextbykey)
  - [`setMarkByKey`](#setmarkbykey)
  - [`setNodeByKey`](#setnodebykey)
  - [`splitNodeByKey`](#splitnodebykey)
  - [`unwrapInlineByKey`](#unwrapinlinebykey)
  - [`unwrapBlockByKey`](#unwrapblockbykey)
  - [`unwrapNodeByKey`](#unwrapnodebykey)
  - [`wrapBlockByKey`](#wrapblockbykey)
  - [`wrapInlineByKey`](#wrapinlinebykey)
- [Document Changes](#document-changes)
  - [`deleteAtRange`](#deleteatrange)
  - [`deleteBackwardAtRange`](#deletebackwardatrange)
  - [`deleteForwardAtRange`](#deleteforwardatrange)
  - [`insertBlockAtRange`](#insertblockatrange)
  - [`insertFragmentAtRange`](#insertfragmentatrange)
  - [`insertInlineAtRange`](#insertinlineatrange)
  - [`insertTextAtRange`](#inserttextatrange)
  - [`addMarkAtRange`](#addmarkatrange)
  - [`setBlockAtRange`](#setblockatrange)
  - [`setInlineAtRange`](#setinlineatrange)
  - [`splitBlockAtRange`](#splitblockatrange)
  - [`splitInlineAtRange`](#splitinlineatrange)
  - [`removeMarkAtRange`](#removemarkatrange)
  - [`toggleMarkAtRange`](#togglemarkatrange)
  - [`unwrapBlockAtRange`](#unwrapblockatrange)
  - [`unwrapInlineAtRange`](#unwrapinlineatrange)
  - [`wrapBlockAtRange`](#wrapblockatrange)
  - [`wrapInlineAtRange`](#wrapinlineatrange)
  - [`wrapTextAtRange`](#wraptextatrange)
- [History Changes](#history-changes)
  - [`redo`](#redo)
  - [`undo`](#undo)


## Properties

### `state`

当前 change 所应用到的 [`State`](./state.md)。 每次执行新的 change 函数，都会更新该属性。


## Methods

### `apply`
`apply(options: Object) => Change`

应用当前 change 步骤，若需要则将其保存到历史记录中。

### `call`
`call(customChange: Function, ...arguments) => Change`

该方法调用 `customChange` 函数，将当前的 `Change` 对象实例作为第一个参数传入，并传入剩余的其它参数。

`customChange` 的函数签名为：

`customChange(change: Change, ...arguments)`

`call` 的作用是允许编写自定义的 change 函数并使其支持链式调用。例如：

```
return state.change()
  .call(myCustomInsertTableChange, columns, rows)
  .focus()
  .apply()
```

## Current State Changes

### `deleteBackward`
`deleteBackward(n: Number) => Change`

在当前光标位置向后删除 `n` 个字符。若选择范围已扩展，这个方法等价于一个普通的 [`delete()`](#delete)。`n` 默认为 `1`。

### `deleteForward`
`deleteForward(n: Number) => Change`

在当前光标位置向前删除 `n` 个字符。若选择范围已扩展，这个方法等价于一个普通的 [`delete()`](#delete)。`n` 默认为 `1`。

### `delete`
`delete() => Change`

删除当前选择范围内的所有内容。

### `insertBlock`
`insertBlock(block: Block) => Change` <br/>
`insertBlock(properties: Object) => Change` <br/>
`insertBlock(type: String) => Change`

在与当前 block 同深度的位置插入一个新 block，若当前 block 非空，将其切分为两个 block 以为其腾出空间。若选择范围已扩展，则先将其删除。

### `insertFragment`
`insertFragment(fragment: Document) => Change`

在当前选择位置插入一个 [`fragment`](./document.md)。若选择范围已扩展，则先将其删除。

### `insertInline`
`insertInline(inline: Inline) => Change` <br/>
`insertInline(properties: Object) => Change`

在当前光标位置插入一个新 inline，若当前 text 非空，将其切分为两个以为其腾出空间。若选择范围已扩展，则先将其删除。

### `insertText`
`insertText(text: String) => Change`

在当前选择位置插入一个 text 字符串。若选择范围已扩展，则先将其删除。

### `addMark`
`addMark(mark: Mark) => Change` <br/>
`addMark(properties: Object) => Change` <br/>
`addMark(type: String) => Change`

为当前选择范围的字符添加 [`mark`](./mark.md)。为方便起见，你可以传入 `type` 字符串或 `properties` 对象来隐式地创建一个该类型的 [`Mark`](./mark.md)。

### `setBlock`
`setBlock(properties: Object) => Change` <br/>
`setBlock(type: String) => Change`

根据 `properties` 设置当前选择范围内 [`Block`](./block.md)。为方便起见，你可以传入 `type` 字符串作为该 block 的类型。

### `setInline`
`setInline(properties: Object) => Change` <br/>
`setInline(type: String) => Change`

根据 `properties` 设置当前选择范围内 [`Inline`](./inline.md)。为方便起见，你可以传入 `type` 字符串作为该 inline 的类型。

### `splitBlock`
`splitBlock(depth: Number) => Change`

根据 `depth` 级别来切分当前选择范围内的 [`Block`](./block.md)。若选择范围已扩展，则先将其删除。`depth` 默认为 `1`。

### `splitInline`
`splitInline(depth: Number) => Change`

根据 `depth` 级别来切分当前选择范围内的 [`Inline`](./block.md)。若选择范围已扩展，则先将其删除。`depth` 默认为 `Infinity`。

### `removeMark`
`removeMark(mark: Mark) => Change` <br/>
`removeMark(properties: Object) => Change` <br/>
`removeMark(type: String) => Change`

移除当前选择范围字符的 [`mark`](./mark.md)。为方便起见，你可以传入 `type` 字符串或 `properties` 对象来隐式地创建一个该类型的 [`Mark`](./mark.md)。

### `toggleMark`
`toggleMark(mark: Mark) => Change` <br/>
`toggleMark(properties: Object) => Change` <br/>
`toggleMark(type: String) => Change`

添加或删除当前选择范围字符的 [`mark`](./mark.md)，依据是其是否存在于选择范围内。为方便起见，你可以传入 `type` 字符串或 `properties` 对象来隐式地创建一个该类型的 [`Mark`](./mark.md)。

### `unwrapBlock`
`unwrapBlock([type: String], [data: Data]) => Change`

根据对 `type` 和 / 或 `data` 的匹配，解包所有当前选择范围内的 [`Block`](./block.md) 节点。

### `unwrapInline`
`unwrapInline([type: String], [data: Data]) => Change`

根据对 `type` 和 / 或 `data` 的匹配，解包所有当前选择范围内的 [`Inline`](./block.md) 节点。

### `wrapBlock`
`wrapBlock(type: String, [data: Data]) => Change`

使用类型为 `type` 的新 [`Block`](./block.md) 将当前选择范围内的 [`Block`](./block.md) 节点包裹起来，可附加 `data` 信息。

### `wrapInline`
`wrapInline(type: String, [data: Data]) => Change`

使用类型为 `type` 的新 [`Inline`](./inline.md) 将当前选择范围内的 [`Inline`](./inline.md) 节点包裹起来，可附加 `data` 信息。

### `wrapText`
`wrapText(prefix: String, [suffix: String]) => Change`

将当前选择范围内的文本用 `prefix` 和 `suffix` 字符串围起来。若省略了 `suffix`，将使用 `prefix` 替代之。


## Selection Changes

### `blur`
`blur() => Change`

使当前选择范围失去焦点。

### `collapseTo{Edge}`
`collapseTo{Edge}() => Change`

将当前选择范围收缩至其 `{Edge}`。`{Edge}` 可为 `Anchor`、`Focus`、`Start` 或 `End`。

### `collapseTo{Edge}Of`
`collapseTo{Edge}Of(node: Node) => Change`

将当前选择范围收缩至 `node` 的 `{Edge}`。`{Edge}` 可为 `Start` 或 `End`。

### `collapseTo{Edge}Of{Direction}Block`
`collapseTo{Edge}Of{Direction}Block() => Change`

将当前选择范围按照 `{Direction}` 方向收缩至下一个 [`Block`](./block.md) 节点的 `{Edge}`。`{Edge}` 可为 `Start` 或 `End`，`{Direction}` 可为 `Next` 或 `Previous`。

### `collapseTo{Edge}Of{Direction}Text`
`collapseTo{Edge}Of{Direction}Text() => Change`

将当前选择范围按照 `{Direction}` 方向收缩至下一个 [`Text`](./text.md) 节点的 `{Edge}`。`{Edge}` 可为 `Start` 或 `End`，`{Direction}` 可为 `Next` 或 `Previous`。

### `extend`
`extend(n: Number) => Change`

将当前选择范围扩展 `n` 个字符。`n` 的正负用于指示方向。

### `extendTo{Edge}Of`
`extendTo{Edge}Of(node: Node) => Change`

将当前选择范围扩展至 `node` 的 `{Edge}`。`{Edge}` 可为 `Start` 或 `End`。

### `flip`
`flip() => Change`

翻转当前选择范围。

### `focus`
`focus() => Change`

使当前选择范围获得焦点。

### `move`
`move(n: Number) => Change`

根据 `n` 移动当前选择范围的偏移量。

### `move{Edge}`
`move{Edge}(n: Number) => Change`

根据 `n` 移动当前选择范围 `{Edge}` 的偏移量。`{Edge}` 可为 `Start` 或 `End`。

### `moveOffsetsTo`
`moveOffsetsTo(anchorOffset: Number, focusOffset: Number) => Change`

将当前选择范围移动至新的 `anchorOffset` 和 `focusOffset`。

### `moveToRangeOf`
`moveToRangeOf(node: Node) => Change`

将当前选择范围的锚点移动至 `node` 的开始，焦点移动至 `node` 的结束。

### `select`
`select(properties: Selection || Object) => Change`

使用合并的 `properties` 更新当前选择范围。`properties` 可为 [`Selection`](./selection.md) 对象或带 selection 相关属性的原生 JS 对象。

### `selectAll`
`selectAll() => Change`

选择当前全部文档内容并获得焦点。

### `deselect`
`deselect() => Change`

取消选择。


## Node Changes

### `addMarkByKey`
`addMarkByKey(key: String, offset: Number, length: Number, mark: Mark) => Change`

根据 [`Node`](./node.md) 的 `key`，为其中由 `offset` 开始的 `length` 个字符添加 `mark`。

### `insertNodeByKey`
`insertNodeByKey(key: String, index: Number, node: Node) => Change`

根据父 [`Node`](./node.md) 的 `key`，在其中的 `index` 位置插入 `node`。

### `insertFragmentByKey`
`insertFragmentByKey(key: String, index: Number, fragment: Fragment) => Transform`

根据父 [`Node`](./node.md) 的 `key`，在其中的 `index` 位置插入 [`Fragment`](./fragment.md)。

### `insertTextByKey`
`insertTextByKey(key: String, offset: Number, text: String, [marks: Set]) => Change`

根据父 [`Text Node`](./text.md) 的 `key`，在其中从 `offset` 起的位置插入带可选 `marks` 的 `text`。

### `moveNodeByKey`
`moveNodeByKey(key: String, newKey: String, newIndex: Number) => Change`

根据 [`Node`](./node.md) 的 `key`，将其移动到带 `newKey` 的新父节点下，相应下标为 `newIndex`。

### `removeMarkByKey`
`removeMarkByKey(key: String, offset: Number, length: Number, mark: Mark) => Change`

根据 [`Node`](./node.md) 的 `key`，移除其中从 `offset` 开始 `length` 个字符的 `mark`。 

### `removeNodeByKey`
`removeNodeByKey(key: String) => Change`

根据 [`Node`](./node.md) 的 `key`，将其从文档中移除。

### `removeTextByKey`
`removeTextByKey(key: String, offset: Number, length: Number) => Change`

根据 [`Node`](./node.md) 的 `key`，移除其中从 `offset` 开始 `length` 个字符的文本。

### `setMarkByKey`
`setMarkByKey(key: String, offset: Number, length: Number, mark: Mark, properties: Object) => Change`

根据 [`Node`](./node.md) 的 `key`，为其设置 `properties` 词典对应的 [`mark`](./mark.md)。

### `setNodeByKey`
`setNodeByKey(key: String, properties: Object) => Change` <br/>
`setNodeByKey(key: String, type: String) => Change`

根据 [`Node`](./node.md) 的 `key`，为其设置 `properties` 词典。为方便起见，你可以传入 `type` 字符串或 `properties` 对象。

### `splitNodeByKey`
`splitNodeByKey(key: String, offset: Number) => Change`

根据从 `offset` 开始的 `key` 切分节点。

### `unwrapInlineByKey`
`unwrapInlineByKey(key: String, properties: Object) => Change` <br/>
`unwrapInlineByKey(key: String, type: String) => Change`

根据 [`Inline`](./inline.md) 的 `key`，解包其中匹配 `properties` 的节点。为方便起见，你可以传入 `type` 字符串或 `properties` 对象。

### `unwrapBlockByKey`
`unwrapBlockByKey(key: String, properties: Object) => Change` <br/>
`unwrapBlockByKey(key: String, type: String) => Change`

根据 [`Block`](./block.md) 的 `key`，解包其中匹配 `properties` 的节点。为方便起见，你可以传入 `type` 字符串或 `properties` 对象。

### `unwrapNodeByKey`
`unwrapNodeByKey(key: String) => Change`

根据 `key`，将节点从其父节点中解包出来。若该节点前后存在兄弟节点，则切分其父节点。若该节点为唯一子节点，则将父节点移除并替换为该节点。不支持解包根节点。

### `wrapBlockByKey`
`wrapBlockByKey(key: String, properties: Object) => Change` <br/>
`wrapBlockByKey(key: String, type: String) => Change`

将满足 `properties` 的给定节点包裹在 [`Block`](./block.md) 节点中。为方便起见，你可以传入 `type` 字符串或 `properties` 对象。

### `wrapInlineByKey`
`wrapInlineByKey(key: String, properties: Object) => Change` <br/>
`wrapInlineByKey(key: String, type: String) => Change`

将满足 `properties` 的给定节点包裹在 [`Inline`](./inline.md) 节点中。为方便起见，你可以传入 `type` 字符串或 `properties` 对象。

## Document Changes

### `deleteBackwardAtRange`
`deleteBackwardAtRange(range: Selection, n: Number) => Change`

在 `range` 中向后删除 `n` 个字符。若 `range` 处于展开状态，该方法等价于普通的 [`delete()`](#delete)。`n` 默认为 `1`。

### `deleteForwardAtRange`
`deleteForwardAtRange(range: Selection, n: Number) => Change`

在 `range` 中向前删除 `n` 个字符。若 `range` 处于展开状态，该方法等价于普通的 [`delete()`](#delete)。`n` 默认为 `1`。

### `deleteAtRange`
`deleteAtRange(range: Selection, ) => Change`

删除 `range` 中全部内容。

### `insertBlockAtRange`
`insertBlockAtRange(range: Selection, block: Block) => Change` <br/>
`insertBlockAtRange(range: Selection, properties: Object) => Change` <br/>
`insertBlockAtRange(range: Selection, type: String) => Change`

在 `range` 中与叶子 block 相同深度的位置，插入新 block。若当前 block 非空，将其切分以腾出空间。若选择范围已扩展，则先将其删除。

### `insertFragmentAtRange`
`insertFragmentAtRange(range: Selection, fragment: Document) => Change`

在 `range` 中插入一个 [`fragment`](./document.md)。若当前 block 非空，将其切分以腾出空间。若选择范围已扩展，则先将其删除。

### `insertInlineAtRange`
`insertInlineAtRange(range: Selection, inline: Inline) => Change` <br/>
`insertInlineAtRange(range: Selection, properties: Object) => Change`

在 `range` 中插入一个 inline 节点。若当前文本非空，将其切分以腾出空间。若选择范围已扩展，则先将其删除。

### `insertTextAtRange`
`insertTextAtRange(range: Selection, text: String) => Change`

在 `range` 中插入一个 `text` 字符串。若选择范围已扩展，则先将其删除。

### `addMarkAtRange`
`addMarkAtRange(range: Selection, mark: Mark) => Change` <br/>
`addMarkAtRange(range: Selection, properties: Object) => Change` <br/>
`addMarkAtRange(range: Selection, type: String) => Change`

在 `range` 中添加 [`mark`](./mark.md)。为方便起见，你可以传入 `type` 字符串或 `properties` 对象来隐式地创建一个该类型的 [`Mark`](./mark.md)。

### `setBlockAtRange`
`setBlockAtRange(range: Selection, properties: Object) => Change` <br/>
`setBlock(range: Selection, type: String) => Change`

在 `range` 中设置 [`Block`](./block.md) 的 `properties`。为方便起见，你可以传入 `type` 字符串来设置 block 类型。

### `setInlineAtRange`
`setInlineAtRange(range: Selection, properties: Object) => Change` <br/>
`setInline(range: Selection, type: String) => Change`

在 `range` 中设置 [`Inline`](./inline.md) 的 `properties`。为方便起见，你可以传入 `type` 字符串来设置 inline 类型。

### `splitBlockAtRange`
`splitBlockAtRange(range: Selection, depth: Number) => Change`

根据 `depth` 级别来切分 `range` 内的 [`Block`](./block.md)。若选择范围已扩展，则先将其删除。`depth` 默认为 `1`。

### `splitInlineAtRange`
`splitInlineAtRange(range: Selection, depth: Number) => Change`

根据 `depth` 级别来切分 `range` 内的 [`Inline`](./inline.md)。若选择范围已扩展，则先将其删除。`depth` 默认为 `Infinity`。

### `removeMarkAtRange`
`removeMarkAtRange(range: Selection, mark: Mark) => Change` <br/>
`removeMarkAtRange(range: Selection, properties: Object) => Change` <br/>
`removeMarkAtRange(range: Selection, type: String) => Change`

移除 `range` 内字符的 [`mark`](./mark.md)。为方便起见，你可以传入 `type` 字符串或 `properties` 对象来隐式地创建一个该类型的 [`Mark`](./mark.md)。

### `toggleMarkAtRange`
`toggleMarkAtRange(range: Selection, mark: Mark) => Change` <br/>
`toggleMarkAtRange(range: Selection, properties: Object) => Change` <br/>
`toggleMarkAtRange(range: Selection, type: String) => Change`

添加或删除 `range` 内字符的 [`mark`](./mark.md)，依据是其是否存在于选择范围内。为方便起见，你可以传入 `type` 字符串或 `properties` 对象来隐式地创建一个该类型的 [`Mark`](./mark.md)。

### `unwrapBlockAtRange`
`unwrapBlockAtRange(range: Selection, properties: Object) => Change` <br/>
`unwrapBlockAtRange(range: Selection, type: String) => Change`

根据对 `properties` 的匹配，解包 `range` 内的 [`Block`](./block.md) 节点。为方便起见，你可以传入 `type` 字符串或 `properties` 对象。

### `unwrapInlineAtRange`
`unwrapInlineAtRange(range: Selection, properties: Object) => Change` <br/>
`unwrapInlineAtRange(range: Selection, type: String) => Change`

根据对 `properties` 的匹配，解包 `range` 内的 [`Inline`](./inline.md) 节点。为方便起见，你可以传入 `type` 字符串或 `properties` 对象。

### `wrapBlockAtRange`
`wrapBlockAtRange(range: Selection, properties: Object) => Change` <br/>
`wrapBlockAtRange(range: Selection, type: String) => Change`

将 `range` 内的 [`Block`](./block.md) 包裹为一个具备 `properties` 的新 [`Block`](./block.md)。为方便起见，你可以传入 `type` 字符串或 `properties` 对象。

### `wrapInlineAtRange`
`wrapInlineAtRange(range: Selection, properties: Object) => Change` <br/>
`wrapInlineAtRange(range: Selection, type: String) => Change`

将 `range` 内的 [`Inline`](./inline.md) 包裹为一个具备 `properties` 的新 [`Inline`](./inline.md)。为方便起见，你可以传入 `type` 字符串或 `properties` 对象。

### `wrapTextAtRange`
`wrapTextAtRange(range: Selection, prefix: String, [suffix: String]) => Change`

将 `range` 内的文本用 `prefix` 和 `suffix` 字符串围起来。若省略了 `suffix`，将使用 `prefix` 替代之。


## History Changes

### `redo`
`redo() => Change`

在历史记录中前进一步。

### `undo`
`undo() => Change`

在历史记录中后退一步。
