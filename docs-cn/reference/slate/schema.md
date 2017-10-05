
# Schemas

每个 Slate 编辑器均有一个与之相关联的 "schema"，其中包含了与编辑器内容结构相关的信息。你可以通过它指定如何渲染不同类型的节点。在更复杂的情景下，你还可以制定规则来强制保证编辑器中内容的合法性。

- [Properties](#properties)
  - [`marks`](#marks)
  - [`nodes`](#nodes)
  - [`rules`](#rules)
- [Rule Properties](#rule-properties)
  - [`decorate`](#decorate)
  - [`match`](#match)
  - [`normalize`](#normalize)
  - [`render`](#render)
  - [`validate`](#validate)
- [Static Methods](#static-methods)
  - [`Schema.isSchema`](#schemaisschema)


## Properties

```js
{
  nodes: Object,
  marks: Object,
  rules: Array
}
```

Schema 中的顶级属性提供的其实都是定义 `rules` 约束的方式。`nodes` 与 `marks` 属性都只是用于定义常用规则子集的快捷方式。

### `marks`
`Object type: Component || Function || Object || String`

```js
{
  bold: props => <strong>{props.children}</strong>
}
```
```js
{
  bold: {
    fontWeight: 'bold'
  }
}
```
```js
{
  bold: 'my-bold-class-name'
}
```

通过 `type` 来定义 schema 中 [`Marks`](./mark.md) 的对象。对象中每个 key 均指向相应 `type` 的 mark。对象的值定义了 Slate 渲染 mark 的方式，它可以是 React 组件、样式对象，或 class 名。

### `nodes`
`Object<type, Component || Function>` <br/>
`Object<type, Rule>`

```js
{
  quote: props => <blockquote {...props.attributes}>{props.children}</blockquote>
}
```
```js
{
  code: {
    render: props => <pre {...props.attributes}><code>{props.children}</code></pre>,
    decorate: myCodeHighlighter
  }
}
```

通过 `type` 定义 schema 中 [`Block`](./block.md) 和 [`Inline`](./inline.md) 节点的对象。对象中每个 key 均指向相应 `type` 的 node。对象的值定义了 Slate 渲染 node 的方式，也可以为其定义其它 schema `Rule` 支持的属性。

### `rules`
`Array<Rule>`

```js
[
  {
    match: { kind: 'block', type: 'code' },
    render: props => <pre {...props.attributes}><code>{props.children}</code></pre>,
    decorate: myCodeHighlighter
  }
]
```

定义 schema 行为的规则数组。将依序对每条规则求值来判断是否匹配。

在内部，schema 中的 `marks` 和 `nodes` 属性都转换为了 `rules`。


## Rule Properties

```js
{
  match: Function,
  decorate: Function,
  normalize: Function,
  render: Component || Function || Object || String,
  validate: Function
}
```

Slate schema 是基于一系列规则的。每条规则都会基于其定义的属性，为 schema 添加特定的功能。

### `match`
`Function match(object: Node || Mark)`

```js
{
  match: (object) => object.kind == 'block' && object.type == 'code'
}
```

`match` 是规则中仅有的必选属性。它用于判断规则对哪些对象应用。

### `decorate`
`Function decorate(text: Node, object: Node) => List<Characters>`

```js
{
  decorate: (text, node) => {
    let { characters } = text
    let first = characters.get(0)
    let { marks } = first
    let mark = Mark.create({ type: 'bold' })
    marks = marks.add(mark)
    first = first.merge({ marks })
    characters = characters.set(0, first)
    return characters
  }
}
```

你可以通过 `decorate` 属性定义额外的 mark 以应用到 node 中的文本范围上。调用它的方式是传入 [`Text`](./text.md) 节点和被匹配的节点。它将返回带有所需 marks 的字符列表，而后在渲染前添加到文本上。

### `normalize`
`Function normalize(change: Change, object: Node, failure: Any) => Change`

```js
{
  normalize: (change, node, invalidChildren) => {
    invalidChildren.forEach((child) => {
      change.removeNodeByKey(child.key)
    })

    return change
  }
}
```

`normalize` 属性是在规则 `validate` 校验失败后，用于恢复编辑器状态的函数。它既接受用于更改状态的 [`Change`](./change.md)，也接受 `validate` 函数校验失败时的返回值，以便于快速判断校验失败的原因。

### `render`
`Component` <br/>
`Function` <br/>
`Object` <br/>
`String`

```js
{
  render: (props) => <pre {...props.attributes}><code>{props.children}</code></pre>
}
```

`render` 属性指定了 Slate 用于渲染 [`Node`](./node.md) 和 [`Mark`](./mark.md) 的 React 组件。为方便起见，Mark 的渲染器亦可指定为样式对象或 class 名字符串。

### `validate`
`Function validate(object: Node) => Any || Void`

```js
{
  validate: (node) => {
    const invalidChildren = node.nodes.filter(child => child.kind == 'block')
    return invalidChildren.size ? invalidChildren : null
  }
}
```

`validate` 属性定义了所匹配对象所必须满足的约束。它在目标对象有效时返回 `Void`，在其无效时返回非空值。这种方式更易于返回对象无效的原因，从而简化了使用 `normalize` 属性从无效状态中恢复的过程。

## Static Methods

### `Schema.isSchema`
`Schema.isSchema(maybeSchema: Any) => Boolean`

返回传入的参数是否为 `Schema` 的 boolean 值。
