
# Utils

```js
import { findDOMNode } from 'slate-react'
```

为 Slate 提供的 React 专用工具函数。特定情况下可能有用。

- [`findDOMNode`](#finddomnode)


## Functions

### `findDOMNode`
`findDOMNode(node: Node) => DOMElement`

寻找 Slate [`Node`](../slate/node.md) 对应的 DOM 节点。仿照 React 内置的 `findDOMNode` 辅助函数设计。
