
# `Data`

```js
import { Data } from 'slate'
```

Data 只是 [`Immutable.Map`](https://facebook.github.io/immutable-js/docs/#/Map) 的薄封装，以避免直接依赖 Immutable，并保证向后兼容性。

一个 data 对象可拥有任意的属性。

- [Static Methods](#static-methods)
  - [`Data.create`](#datacreate)
  - [`Data.fromJSON`](#datafromjson)


## Static Methods

### `Data.create`
`Data.create(properties: Object) => Data`

由原生 JS `properties` 对象构造出一个 data 对象。

### `Data.fromJSON`
`Data.fromJSON(object: Object) => Data`

由 JSON `object` 构造出一个 data 对象。
