
# `<Editor>`

```js
import { Editor } from 'slate-react'
```

渲染 Slate 编辑器的顶层 React 组件。

- [Properties](#properties)
  - [`autoCorrect`](#autocorrect)
  - [`autoFocus`](#autofocus)
  - [`className`](#classname)
  - [`onChange`](#onchange)
  - [`plugins`](#plugins)
  - [`readOnly`](#readonly)
  - [`role`](#role)
  - [`spellCheck`](#spellcheck)
  - [`state`](#state)
  - [`style`](#style)
  - [`tabIndex`](#tabindex)
- [Placeholder Properties](#placeholder-properties)
  - [`placeholder`](#placeholder)
  - [`placeholderClassName`](#placeholderclassname)
  - [`placeholderStyle`](#placeholderstyle)
- [Plugin-like Properties](#plugin-like-properties)
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
  - [`schema`](#schema)
- [Methods](#methods)
  - [`blur`](#blur)
  - [`focus`](#focus)
  - [`getSchema()`](#getschema)
  - [`getState()`](#getstate)
  - [`onChange(change)`](#onchange)
  - [`change`](#change)

## Properties

```js
<Editor
  autoCorrect={Boolean}
  autoFocus={Boolean}
  className={String}
  onChange={Function}
  plugins={Array}
  readOnly={Boolean}
  role={String}
  spellCheck={Boolean}
  state={State}
  style={Object}
  tabIndex={Number}
/>
```

### `autoCorrect`
`Boolean`

编辑器是否尝试自动更正拼写错误。

### `autoFocus`
`Boolean`

可选值。当设置为 true 时，尝试在 content editable 元素加载至页面时自动使其获得焦点。

### `className`
`String`

应用到 content editable 元素上可选的 class 名。

### `onChange`
`Function onChange(change: Change)`

传入 `change` 以应用变更的回调。一般而言你需要将新的 `change.state` 通过编辑器的 `state` 属性传入。这个钩子允许你在此为编辑器添加持久化逻辑。

### `plugins`
`Array`

定义编辑器行为的 [`Plugins`](./plugins.md) 数组。

### `readOnly`
`Boolean`

编辑器是否应处于 "read-only" 模式。这时渲染方式保持不变，但用户不能编辑内容。

### `spellCheck`
`Boolean`

是否为编辑器启用拼写检查。

### `role`
`String`

定义编辑器 role 的 ARIA 属性，在可编辑时默认为 `textbox`。

### `state`
`State`

代表当前编辑器状态的 [`State`](../slate/state.md) 对象。

### `style`
`Object`

应用到 content editable 元素中的可选样式字典。

### `tabIndex`
`Number`

指示其是否参与[序列键盘导航](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex)。

## Placeholder Properties

```js
<Editor
  placeholder={String || Element}
  placeholderClassName={String}
  placeholderStyle={Object}
/>
```

### `placeholder`
`String || Element`

作为默认 block 类型占位符的字符串（或 React 组件）。

### `placeholderClassName`
`String`

应用到 block 类型占位符的可选 class 名。

### `placeholderStyle`
`Object`

应用到 block 类型占位符的可选样式字典。若 `placeholder` 为字符串且未传递 class 名与样式字典，该属性默认为 `{ opacity: '0.333' }`。


## Plugin-like Properties

在自有属性基础上，编辑器也允许传入任意由 [plugin](./plugins.md) 定义的属性。

这些属性实际上都仅为便捷起见的隐式插件定义。在内部，它们将组合为一个在插件栈上具备最高优先级的插件。

例如，如下的代码示例是等价的：

```js
const plugins = [
  somePlugin
]

<Editor
  onKeyDown={myKeyHandler}
  plugins={plugins}
  state={state}
/>
```

```js
const editorPlugin = {
  onKeyDown: myKeyHandler
}

const plugins = [
  editorPlugin,
  somePlugin
]

<Editor
  plugins={plugins}
  state={state}
/>
```

### `onBeforeInput`
### `onBlur`
### `onFocus`
### `onCopy`
### `onCut`
### `onDrop`
### `onKeyDown`
### `onKeyUp`
### `onPaste`
### `onSelect`
### `schema`

要查看这些属性的行为，请查阅 [Plugins](./plugins.md) 文档。


## Methods

### `blur`
`blur() => Void`

编程式地使编辑器失去焦点。

### `focus`
`focus() => Void`

编程式地使编辑器获得焦点。

### `getSchema`
`getSchema() => Schema`

返回编辑器的当前 schema。

### `getState`
`getState() => State`

返回编辑器的当前 state。

### `onChange`
`onChange(change: Change) => Void`

触发该方法时将使用 `change` 更新编辑器状态。在其成为编辑器的新 `state` 前，它将在全部插件中传递一遍，并传入父组件。
