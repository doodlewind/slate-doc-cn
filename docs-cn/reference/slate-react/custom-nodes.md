
# Custom Nodes

Slate 根据传入的 schema，为 [`Block`](../slate/block.md) 和 [`Inline`](../slate/inline.md) 渲染自定义节点。这允许你完全地定制 Slate 编辑器的渲染行为。

- [Properties](#properties)
  - [`attributes`](#attributes)
  - [`children`](#children)
  - [`editor`](#editor)
  - [`isSelected`](#isselected)
  - [`node`](#node)
  - [`parent`](#parent)
  - [`readOnly`](#readonly)
  - [`state`](#state)

## Properties

```js
<{Custom}
  attributes={Object}
  children={Object}
  editor={Editor}
  isSelected={Boolean}
  node={Node}
  parent={Node}
  readOnly={Boolean}
  state={State}
/>
```

### `attributes`
`Object`

DOM 属性字典，必须添加到所渲染节点对应的主 DOM 元素上。例如：

```js
return (
  <p {...props.attributes}>{props.children}</p>
)
```
```js
return (
  <figure {...props.attributes}>
    <img src={...} />
  </figure>
)
```

### `children`
`Object`

由 Slate 内部组件组成的 React 子元素集合，它为你处理编辑器内所有的编辑逻辑。你必须在非空节点中将它们作为子节点渲染。例如：

```js
return (
  <p {...props.attributes}>
    {props.children}
  </p>
)
```

### `editor`
`Editor`

对 Slate [`<Editor>`](./editor.md) 实例的引用。这允许你获得当前的编辑器 `state`，或对 state 执行 `change`。例如：

```js
const state = editor.getState()
```
```js
editor.change((change) => {
  change.selectAll().delete()
})
```

### `isSelected`
`Boolean`

表示当前渲染节点是否选中的 boolean。你可以将其用于可视化地渲染选择范围。

### `node`
`Node`

对当前渲染 [`Node`](../slate/node.md) 的引用。

### `parent`
`Node`

对当前渲染 [`Node`](../slate/node.md) 父节点的引用。

### `readOnly`
`Boolean`

编辑器是否处于 "read-only" 模式，这时渲染方式保持不变，但用户不能编辑内容。

### `state`
`State`

对当前编辑器 [`State`](../slate/state.md) 的引用。
