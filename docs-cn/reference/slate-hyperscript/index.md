
# `slate-hyperscript`

```js
import h from 'slate-hyperscript'
import { createHyperscript } from 'slate-hyperscript'
```

帮助你使用 JSX 编写 Slate 文档的 hyperscript 工具！

- [Example](#example)
- [Exports](#methods)
  - [`h`](#h)
  - [`createHyperscript`](#createhyperscript)


## Example

```js
/** @jsx h */

import h from 'slate-hyperscript'

const state = (
  <state>
    <document>
      <block type="paragraph">
        A string of <mark type="bold">bold</mark> in a <inline type="link" data={{ src: 'http://slatejs.org' }}>Slate</inline> editor!
      </block>
      <block type="image" data={{ src: 'https://...' }} isVoid />
    </document>
  </state>
)
```

```js
/** @jsx h */

import { createHyperscript } from 'slate-hyperscript'

const h = createHyperscript({
  blocks: {
    paragraph: 'paragraph',
    image: {
      type: 'image',
      isVoid: true,
    }
  },
  inlines: {
    link: 'link',
  },
  marks: {
    b: 'bold',
  },
})

const state = (
  <state>
    <document>
      <paragraph>
        A string of <b>bold</b> in a <link src="http://slatejs.org">Slate</link> editor!
      </paragraph>
      <image src="https://..." />
    </document>
  </state>
)
```


## Exports

### `h`
`Function`

默认情况下 `slate-hyperscript` 导出的是一个简单的 hyperscript 辅助函数，你能够直接用它来创建 Slate 对象。

### `createHyperscript`
`createHyperscript(options: Object) => Function`

另一个导出的内容是 `createHyperscript` 辅助函数，你可以用它来创建你自己的、更智能的、与 schema 相关的 hyperscript 辅助函数。你可以为其传入提供 schema 信息的 `options`，从而更加简练地创建对象。

```js
{
  blocks: Object,
  inlines: Object,
  marks: Object,
  creators: Object,
}
```

