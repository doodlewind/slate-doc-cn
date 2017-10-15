
# Plugins

插件可挂载到编辑器上，以不同方式更改其行为。插件是简单的 JS 对象，包含控制不同行为的属性——事件处理、change 处理、渲染等。

每个编辑器都有一个带特定顺序的插件【中间件栈】。

当编辑器需解析插件相关回调时，它将遍历插件栈，搜索第一个返回了值的插件。在获得该值后，编辑器 **不会** 继续搜索剩余插件，而是尽早返回。如果你希望继续栈上操作，插件回调需要返回 `undefined`。

- [Conventions](#conventions)
- [Event Handler Properties](#event-handle-properties)
  - [`onBeforeInput`](#onbeforeinput)
  - [`onBlur`](#onblur)
  - [`onFocus`](#onfocus)
  - [`onCopy`](#oncopy)
  - [`onCut`](#oncut)
  - [`onDrop`](#ondrop)
  - [`onKeyDown`](#onkeydown)
  - [`onKeyUp`](#onkeyup)
  - [`onPaste`](#onpaste)
  - [`onSelect`](#onselect)
- [Other Properties](#other-properties)
  - [`onChange`](#onchange)
  - [`onBeforeChange`](#onbeforechange)
  - [`render`](#render)
  - [`schema`](#schema)


## Conventions

插件应当总是开放一个接受选项的函数。这样，即便它实际上不需要输入参数，也不会在未来需要传入更多参数时造成破坏性的 API 变更。一个基本的插件可能形如：

```js
export default function MySlatePlugin(options) {
  return {
    // 返回描述自定义逻辑的属性…
  }
}
```


## Event Handler Properties

```js
{
  onBeforeInput: Function,
  onBlur: Function,
  onFocus: Function,
  onCopy: Function,
  onCut: Function,
  onDrop: Function,
  onKeyDown: Function,
  onKeyUp: Function,
  onPaste: Function,
  onSelect: Function
}
```

全部的插件回调属性都会传入和 React 事件回调一致的 React `event` 对象。它们同时传入一个包含相关 Slate 信息的 `data` 对象、当前编辑器 `change`、以及 `editor` 实例本身。

每个事件回调都可以在需要更新编辑器状态时，返回一个新的 `change` 对象。如果没有返回内容，编辑器将继续解析插件栈。

### `onBeforeInput`
`Function onBeforeInput(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调在字符文本插入 `contenteditable` 元素前出发。

如果不希望发生默认的插入行为，请记得 `event.preventDefault()`！如果没有其它插件处理该事件，将由 [Core plugin](./core.md) 处理。

### `onBlur`
`Function onBlur(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调在编辑器 `contenteditable` 元素失去焦点时触发。如果没有其它插件处理该事件，将由 [Core plugin](./core.md) 处理。

### `onFocus`
`Function onFocus(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调在编辑器 `contenteditable` 元素获得焦点时触发。如果没有其它插件处理该事件，将由 [Core plugin](./core.md) 处理。

### `onCopy`
`Function onCopy(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调在编辑器 `contenteditable` 元素中发生复制事件时触发。

`data` 对象包含了 `type` 字符串，及与该类型相关的数据。目前仅支持 `"fragment"` 类型：

```js
{
  type: 'fragment',
  fragment: Document
}
```

如果没有其它插件处理该事件，将由 [Core plugin](./core.md) 处理。

### `onCut`
`Function onCut(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调等价于 `onCopy` 回调。如果没有其它插件处理该事件，将由 [Core plugin](./core.md) 处理。

### `onDrop`
`Function onDrop(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调在用户向 `contenteditable` 元素中放下内容时调用。该事件已禁用默认行为，因此你必须自定义状态变更来实现功能。

`data` 对象是用于将跨浏览器 drop 元数据标准化的便捷对象。每个 data 对象都有一个 `type` 属性，其可为 `text`、`html` 或 `files` 其中之一，也有一个类型为 [`Selection`](../slate/selection.md) 的 `target` 属性来指示在哪里发生 drop 行为。根据类型不同，其结构为：

```js
{
  type: 'text',
  target: Selection,
  text: String
}

{
  type: 'html',
  target: Selection,
  text: String,
  html: String
}

{
  type: 'files',
  target: Selection,
  files: FileList
}
```

如果没有其它插件处理该事件，将由 [Core plugin](./core.md) 处理。

### `onKeyDown`
`Function onKeyDown(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调在 `contenteditable` 元素中按键刚刚被按下、任何其它动作触发前触发。

`data` 对象包含了按键的字符串名称 `key` 及其 `code`。它也包含了一系列有用的工具属性以便于判断热键逻辑。例如，在 `control` 键按下时，`isCtrl` 为真：


```js
{
  key: String,
  code: Number,
  isAlt: Boolean,
  isCmd: Boolean,
  isCtrl: Boolean,
  isLine: Boolean,
  isMeta: Boolean,
  isMod: Boolean,
  isModAlt: Boolean,
  isShift: Boolean,
  isWord: Boolean
}
```

当 Windows 上 `control` 键或 Mac 上 `command` 键按下，且 `alt/option` 键 _没有_ 按下时，`isMod` 布尔值为 `true`。这便于添加 `command+b` 一类的快捷键。

当 Windows 上 `control` 键或 Mac 上 `command` 键按下，且 `alt/option` 键 _有_ 按下时，`isModAlt` 布尔值为 `true`。这便于添加 `command+option+1` 一类的次级快捷键。

`isLine` 与 `isWord` 布尔值代表删除或移动光标时，【行修饰符】与【词修饰符】快捷键是否按下。例如，在 Mac 上 `option + →` 每次将光标向右移动一个词。

如果不希望发生默认的插入行为，请确保调用了 `event.preventDefault()`！如果没有其它插件处理该事件，将由 [Core plugin](./core.md) 处理。

### `onKeyUp`
`Function onKeUp(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调在 `contenteditable` 元素中按键被释放时触发。

`data` 对象中包含了与 `onKeyDown` 中 `data` 对象相同的信息。

### `onPaste`
`Function onPaste(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调在用户向 `contenteditable` 元素中粘贴内容时触发。该事件已禁用默认行为，因此你必须自定义状态变更来实现功能。

`data` 对象是用于将跨浏览器 drop 元数据标准化的便捷对象。每个 data 对象都有一个 `type` 属性，其可为 `text`、`html` 或 `files` 其中之一，也有一个类型为 [`Selection`](../slate/selection.md) 的 `target` 属性来指示在哪里发生 drop 行为。根据类型不同，其结构为：

```js
{
  type: 'text',
  text: String
}

{
  type: 'html',
  text: String,
  html: String
}

{
  type: 'files',
  files: FileList
}
```

如果没有其它插件处理该事件，将由 [Core plugin](./core.md) 处理。

### `onSelect`
`Function onSelect(event: Event, data: Object, change: Change, editor: Editor) => Change || Void`

该回调在原生 DOM 选择范围变更时触发。

`data` 对象包含了表示新选择范围的 [`Selection`](../slate/selection.md) 对象。

如果没有其它插件处理该事件，将由 [Core plugin](./core.md) 处理。

_注意：这 **不是** Slate 内部的选择范围表示（内部选择范围确实镜像了它）。如果你希望在 Slate 中选择范围更改时获得提示，使用 `<Editor>` 的 [`onChange`](../slate-react/editor.md#onchange) 属性。这个回调用于向你开放对底层 DOM 选择范围的控制，而原生行为 **不会** 总是按照你的预期触发。_


## Other Properties

```js
{
  onChange: Function
}
```

### `onChange`
`Function onChange(change: Change) => Any || Void`

`onChange` 回调不是浏览器的原生事件回调，而是在编辑器状态变更时触发。这允许插件根据需要增加 change。

### `onBeforeChange`
`Function onBeforeChange(change: Change) => Change || Void`

`onBeforeChange` 回调不是浏览器的原生事件回调，而是在编辑器接收到新 state 并传播 change 到 `onChange` 前触发。

### `render`
`Function render(props: Object, state: State, editor: Editor) => Object || Void`

`render` 属性允许你定义类似高阶组件的行为。它传入所有编辑器的属性，包括 `props.children`。你可以将已经存在的 `chidren` 用任何自定义元素包裹，或根据需要盖里属性。这在渲染工具栏、为编辑器添加样式、对渲染进行校验等场景时能够有所帮助。记得 `render` 函数需要渲染 `props.children` 以渲染编辑器的子节点。

### `schema`
`Object`

`schema` 属性允许你定义一系列添加到编辑器 schema 中的规则。从各插件 schema 中返回的规则将被整合为一个编辑器的新 schema，其应用规则的顺序与插件栈顺序一致。
