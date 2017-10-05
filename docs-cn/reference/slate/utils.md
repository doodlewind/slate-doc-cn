
# Utils

```js
import {
  resetKeyGenerator,
  setKeyGenerator,
} from 'slate'
```

Slate 附带的实用工具函数，在特定场合下可能有用。

- [`resetKeyGenerator`](#resetkeygenerator)
- [`setKeyGenerator`](#setkeygenerator)


## Functions

### `resetKeyGenerator`
`resetkeygenerator() => Void`

重置 Slate 内置的 key 生成函数至其默认状态。这可用于服务端渲染，或任何需要保证 key 生成过程具备新鲜性与决定性的场合。

### `setKeyGenerator`
`setKeyGenerator(generator: Function) => Void`

可用于指定你自己的 key 生成函数，以取代 Slate 自带的、仅使用自增数字字符串的默认生成器。（例如 `'0'`、`'1'`、`'2'`…）

它会对全局下所有 Slate 的依赖生效。
