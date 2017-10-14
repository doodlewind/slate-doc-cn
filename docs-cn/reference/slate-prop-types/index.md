
# `slate-prop-types`

```js
import Types from 'slate-prop-types'
```

供 Slate 编辑器与插件使用的 React prop types 集合。

- [Example](#example)
- [Exports](#exports)
  - [`block`](#block)
  - [`blocks`](#blocks)
  - [`change`](#change)
  - [`character`](#character)
  - [`characters`](#characters)
  - [`data`](#data)
  - [`document`](#document)
  - [`history`](#history)
  - [`inline`](#inline)
  - [`inlines`](#inlines)
  - [`mark`](#mark)
  - [`marks`](#marks)
  - [`node`](#node)
  - [`nodes`](#nodes)
  - [`range`](#range)
  - [`ranges`](#ranges)
  - [`schema`](#schema)
  - [`selection`](#selection)
  - [`stack`](#stack)
  - [`state`](#state)
  - [`text`](#text)
  - [`texts`](#texts)


## Example

```js
import React from 'react'
import Types from 'slate-prop-types'

class Toolbar extends React.Component {

  propTypes = {
    block: Types.block,
    schema: Types.schema.isRequired,
    state: Types.state.isRequired,
  }

  ...

}
```


## Exports

### `block`

保证值为 Slate `Block`。

### `blocks`

保证值为 Slate [`Block`](../slate/block.md) 对象组成的不可变 `List`。

### `change`

保证值为 Slate `Change`。

### `character`

保证值为 Slate `Character`。

### `characters`

保证值为 Slate `Character` 对象组成的不可变 `List`。

### `data`

保证值为 Slate `Data`。

### `document`

保证值为 Slate `Document`。

### `history`

保证值为 Slate `History`。

### `inline`

保证值为 Slate `Inline`。

### `inlines`

保证值为 Slate [`Inline`](../slate/inline.md) 对象组成的不可变 `List`。

### `mark`

保证值为 Slate `Mark`。

### `marks`

保证值为 Slate `Mark` 对象组成的不可变 `List`。

### `node`

保证值为 Slate `Node`。

### `nodes`

保证值为 Slate `Node` 对象组成的不可变 `List`。

### `range`

保证值为 Slate `Range`。

### `ranges`

保证值为 Slate `Range` 对象组成的不可变 `List`。

### `schema`

保证值为 Slate `Schema`。

### `selection`

保证值为 Slate `Selection`。

### `stack`

保证值为 Slate `Stack`。

### `state`

保证值为 Slate `State`。

### `text`

保证值为 Slate `Text`。

### `texts`

保证值为 Slate `Text` 对象组成的不可变 `List`。
