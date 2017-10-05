
# Core Plugin

Slate 编辑器本身的约束非常少。它默认下仅有的逻辑是与 `contenteditable` 本身功能相关的逻辑——处理文本、选择范围等。这些逻辑包含在一个单独的插件中，其名为 "core" 插件。

- [Default Behavior](#behavior)
- [Overriding Defaults](#overriding-defaults)


## Default Behavior

核心插件的默认行为，将执行下列逻辑：

### `onBeforeInput`

当输入文本时，核心插件将 `event.data` 中的文本插入编辑器中。

### `onBlur`

当编辑器失去焦点时，核心插件更新 Slate 内部的数据模型，并不执行重绘。

### `onFocus`

当编辑器获得焦点时，核心插件更新 Slate 内部的数据模型，并不执行重绘。

### `onCopy`

当用户复制部分文档时，核心插件为所复制文本附带上完整的文档序列化内容后，将其添加到剪贴板，从而能够在粘贴时反序列化并插入文档以保留格式。

### `onCut`

当用户剪切部分文档时，核心插件执行与 `onCopy` 相同的逻辑，但其也会删除当前选中范围下的内容。

### `onDrop`

当用户向编辑器中放下内容时，核心插件将 `text` 与 `html` 类型处理为纯文本，对 `files` 类型不作处理。

### `onKeyDown`

在按键时，核心插件处理部分 `contenteditable` 元素所需执行的【原生】行为。例如在按下 `enter` 时拆分 block、在按下 `backspace` 时移除字符、在按下 `cmd-z` 时触发对历史记录的撤销等。

### `onPaste`

当用户向编辑器中粘贴内容时，核心插件将 `text` 与 `html` 类型的粘贴处理为纯文本，对 `files` 类型不作处理。

### `onSelect`

当用户在 DOM 中选择了新内容时，核心插件更新 Slate 内部数据模型中的选择范围，并按需执行重绘。

### `render`

渲染编辑器中全部默认内容！

### `schema`

核心插件定义的 schema 保证了对内容的若干约束，并定义了默认的 block 和 inline 节点渲染器组件——各自包裹在 `<div>` 和 `<span>` 中。每个组件都包含了避免不必要重绘的 `shouldComponentUpdate` 逻辑。

默认的 block 组件也包含了对其 placeholder 的控制逻辑，这是通过 [`<Editor>`](../slate-react/editor.md) 的 placeholder 选项控制的。


## Overriding Defaults

所有你添加的插件都会覆盖核心插件的默认行为，这是因为核心插件总是在最后解析。

不过，有时候你可能希望在不添加新逻辑的前提下，禁用核心插件的逻辑。例如，你可能想要阻止 `enter` 键执行任何操作。这时，你需要定义一个 "noop" 回调。

一个无操作 `onBeforeInput` 回调形如：

```js
function onBeforeInput(event, data, state) {
  event.preventDefault()
  return state
}
```

注意在上例中调用了 `event.preventDefault()` 来阻止默认浏览器行为，并返回了当前 `state` 来阻止编辑器继续解析其插件栈。
