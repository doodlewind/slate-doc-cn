
# `<Placeholder>`

```js
import { Placeholder } from 'slate-react'
```

为节点添加占位符的简单组件。它封装了所有与 Slate 相关的占位符渲染判断逻辑，这样不需要你来操心了。

- [Properties](#properties)
  - [`children`](#children)
  - [`className`](#className)
  - [`firstOnly`](#firstOnly)
  - [`node`](#node)
  - [`parent`](#parent)
  - [`state`](#state)
  - [`style`](#style)


## Properties

```js
<Placeholder
  className={String}
  node={Node}
  parent={Node}
  state={State}
  style={Object}
>
  {children}
</Placeholder>
```

### `children`
`Any`

渲染到占位符 `<span>` 元素内的 React 子元素。

### `className`
`String`

添加到占位符 `<span>` 元素内的可选 class 名字符串。

### `firstOnly`
`Boolean`

可选的开关，允许 Placeholder 在其不是父节点下第一个 node 时渲染。这可适用于 Placeholder 需要在每个节点空实例下渲染的场景。默认为 `true`。

### `node`
`Node`

占位符元素渲染时的顶级节点。占位符将绝对定位，覆盖整个节点。

### `parent`
`Node`

检查非空内容的节点。在 `firstOnly` 设置为 `false` 时，它用于判断是否需展示占位符。

### `state`
`State`

编辑器的当前状态。

### `style`
`Object`

传递给占位符 `<span>` 元素的可选样式字典。
