
# `Node`

`Node` 不是向外开放的模块，而是由 [`Document`](./document.md)、[`Block`](./block.md) 与 [`Inline`](./inline.md) 共同实现的接口。

- [Properties](#properties)
  - [`nodes`](#nodes)
- [Computed Properties](#computed-properties)
  - [`kind`](#kind)
  - [`text`](#text)
- [Methods](#methods)
  - [`filterDescendants`](#filterdescendants)
  - [`findDescendant`](#finddescendant)
  - [`getBlocksAtRange`](#getblocksatrange)
  - [`getBlocks`](#getblocks)
  - [`getCharactersAtRange`](#getcharactersatrange)
  - [`getChild`](#getchild)
  - [`getClosestBlock`](#getclosestblock)
  - [`getClosestInline`](#getclosestinline)
  - [`getClosest`](#getclosest)
  - [`getDepth`](#getdepth)
  - [`getDescendant`](#getdescendant)
  - [`getFirstText`](#getfirsttext)
  - [`getFragmentAtRange`](#getfragmentatrange)
  - [`getFurthestAncestor`](#getfurthestancestor)
  - [`getFurthestBlock`](#getfurthestblock)
  - [`getFurthestInline`](#getfurthestinline)
  - [`getFurthestOnlyChildAncestor`](#getfurthestonlychildancestor)
  - [`getFurthestBlock`](#getfurthestblock)
  - [`getInlinesAtRange`](#getinlinesatrange)
  - [`getLastText`](#getlasttext)
  - [`getMarksAtRange`](#getmarksatrange)
  - [`getNextBlock`](#getnextblock)
  - [`getNextSibling`](#getnextsibling)
  - [`getNextText`](#getnexttext)
  - [`getParent`](#getparent)
  - [`getPreviousBlock`](#getpreviousblock)
  - [`getPreviousSibling`](#getprevioussibling)
  - [`getPreviousText`](#getprevioustext)
  - [`getTextAtOffset`](#gettextatoffset)
  - [`getTextsAtRange`](#gettextsatrange)
  - [`hasChild`](#haschild)
  - [`hasDescendant`](#hasdescendant)


## Properties

### `key`
`String`

暂时的唯一标记符，用于标记该 node。

默认情况下，key **并不是** 可用于在数据库等场景下对 node 的持久标识。它们仅用于在一个独立的 Slate 实例内部标识 node。因此，它们只是简单的自增字符串。（如 `'0'`、`'1'`、`'2'`……）

如果希望将 key 的唯一性持久化，你需要使用 [`setKeyGenerator`](../utils/utils.md#setkeygenerator) util 以提供自己的 key 生成函数。

### `nodes`
`Immutable.List`

子节点列表。默认为包含单一文本节点的列表。


## Computed Properties

### `kind`
`String`

不可变的 String，值为 `'document'`、`'block'`、`'inline'` 或 `'text'` 以区分节点类型。

### `text`
`String`

该节点的全部子 [`Text`](./text.md) 节点连接成的字符串。


## Methods

### `filterDescendants`
`filterDescendants(iterator: Function) => List`

根据 `iterator` 深度过滤节点的多个后代节点。

### `findDescendant`
`findDescendant(iterator: Function) => Node || Void`

根据 `iterator` 深度查找单个后代节点。

### `getBlocksAtRange`
`getBlocksAtRange(range: Selection) => List`

获取 `range` 中全部最底层的 [`Block`](./block.md) 节点。

### `getBlocks`
`getBlocks() => List`

获取节点后代中全部最底层的 [`Block`](./block.md) 节点。

### `getCharactersAtRange`
`getCharactersAtRange(range: Selection) => List`

获取 `range` 中的 [`Character`](./character.md) 列表。

### `getChild`
`getChild(key: String || Node) => Node || Void`

根据 `key` 获取子节点。

### `getClosestBlock`
`getClosestBlock(key: String || Node) => Node || Void`

根据后代节点的 `key` 获得与其最接近的 [`Block`](./block.md) 节点。

### `getClosestInline`
`getClosestInline(key: String || Node) => Node || Void`

根据后代节点的 `key` 获得与其最接近的 [`Inline`](./inline.md) 节点。

### `getClosest`
`getClosest(key: String || Node, match: Function) => Node || Void`

根据后代节点的 `key` 获得与其最接近且满足 `match` 函数的父节点。

### `getDepth`
`getDepth(key: String || Node) => Number`

根据 `key` 获得后代节点的深度。

### `getDescendant`
`getDescendant(key: String || Node) => Node || Void`

根据 `key` 获取后代节点。

### `getFirstText`
`getFirstText() => Node || Void`

获得节点中第一个 text 子节点。

### `getFragmentAtRange`
`getFragmentAtRange(range: Selection) => Document`

获得节点落在 `range` 中的文档 fragment。

### `getFurthest`
`getFurthest(key: String, iterator: Function) => Node || Null`

根据 `key` 获得匹配 `iterator` 的最远父节点。

### `getFurthestAncestor`
`getFurthestAncestor(key: String) => Node || Null`

根据 `key` 获得最远祖先节点。

### `getFurthestBlock`
`getFurthestBlock(key: String) => Node || Null`

根据 `key` 获得最远的 block 祖先节点。

### `getFurthestInline`
`getFurthestInline(key: String) => Node || Null`

根据 `key` 获得最远的 inline 祖先节点。

### `getFurthestOnlyChildAncestor`
`getFurthestOnlyChildAncestor(key: String) => Node || Null`

根据 `key` 获得仅有一个子节点的最远祖先节点。

### `getInlinesAtRange`
`getInlinesAtRange(range: Selection) => List`

获得 `range` 中所有最顶层的 [`Inline`](./inline.md) 节点。

### `getLastText`
`getLastText() => Node || Void`

获得节点中最后一个子 text 节点。

### `getMarksAtRange`
`getMarksAtRange(range: Selection) => Set`

获得 `range` 中全部 mark 的集合。

### `getNextBlock`
`getNextBlock(key: String || Node) => Node || Void`

获得 `key` 节点后代中的下一个最底层 [`Block`](./block.md) 节点。

### `getNextSibling`
`getNextSibling(key: String || Node) => Node || Void`

根据 `key` 在后代中查找下一个兄弟节点。

### `getNextText`
`getNextText(key: String || Node) => Node || Void`

根据 `key` 获得后代中的下一个 [`Text`](./text.md) 节点。

### `getParent`
`getParent(key: String || Node) => Node || Void`

根据 `key` 获得后代中的父节点。

### `getPreviousBlock`
`getPreviousBlock(key: String || Node) => Node || Void`

获得 `key` 节点后代中的上一个最底层 [`Block`](./block.md) 节点。

### `getPreviousSibling`
`getPreviousSibling(key: String || Node) => Node || Void`

根据 `key` 在后代中查找上一个兄弟节点。

### `getPreviousText`
`getPreviousText(key: String || Node) => Node || Void`

根据 `key` 获得后代中的上一个 [`Text`](./text.md) 节点。

### `getTextAtOffset`
`getTextAtOffset(offset: Number) => Text || Void`

获得 `offset` 位置的 [`Text`](./text.md) 节点。

### `getTextsAtRange`
`getTextsAtRange(range: Selection) => List`

获得 `range` 中所有的 [`Text`](./text.md) 节点。

### `hasChild`
`hasChild(key: String || Node) => Boolean`

检查节点是否有键值为 `key` 的子节点。

### `hasDescendant`
`hasDescendant(key: String || Node) => Boolean`

检查节点是否有键值为 `key` 的后代节点。
