
# `slate-simulator`

```js
import Simulator from 'slate-simulator'
```

为编写 Slate 编辑器与插件测试提供帮助的模拟器。

- [Example](#example)
- [Methods](#methods)
  - [`beforeInput`](#beforeinput)
  - [`blur`](#blur)
  - [`copy`](#copy)
  - [`cut`](#cut)
  - [`drop`](#drop)
  - [`focus`](#focus)
  - [`keyDown`](#keydown)
  - [`paste`](#paste)
  - [`select`](#select)


## Example

```js
import Simulator from 'slate-simulator'

const state = ...
const plugins = [ ... ]
const simulator = new Simulator({ state, plugins })

simulator
  .focus()
  .beforeInput({ data: 'H' })
  .beforeInput({ data: 'e' })
  .beforeInput({ data: 'l' })
  .beforeInput({ data: 'l' })
  .beforeInput({ data: 'o' })
  .beforeInput({ data: '!' })
  .keyDown({}, { key: 'enter' })

const nextState = simulator.state
```


## Methods

### `beforeInput`
`beforeInput(event: Object, data: Object) => Simulator`

以 `event` 对象与 `data` 对象模拟 `beforeinput` 事件。

### `blur`
`blur(event: Object, data: Object) => Simulator`

以 `event` 对象与 `data` 对象模拟 `blur` 事件。

### `copy`
`copy(event: Object, data: Object) => Simulator`

以 `event` 对象与 `data` 对象模拟 `copy` 事件。

### `cut`
`cut(event: Object, data: Object) => Simulator`

以 `event` 对象与 `data` 对象模拟 `cut` 事件。

### `drop`
`drop(event: Object, data: Object) => Simulator`

以 `event` 对象与 `data` 对象模拟 `drop` 事件。

### `focus`
`focus(event: Object, data: Object) => Simulator`

以 `event` 对象与 `data` 对象模拟 `focus` 事件。

### `keyDown`
`keyDown(event: Object, data: Object) => Simulator`

以 `event` 对象与 `data` 对象模拟 `keyDown` 事件。

### `select`
`select(event: Object, data: Object) => Simulator`

以 `event` 对象与 `data` 对象模拟 `select` 事件。

