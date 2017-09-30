
<br/>
<p align="center"><strong>Previous:</strong><br/><a href="./applying-custom-formatting.md">应用自定义格式</a></p>
<br/>

# 使用插件

到目前为止，我们所学习的内容都是在编写为特定的 Slate 编辑器定制的一次性业务逻辑。不过，Slate 最漂亮的地方之一在于它的插件系统，它能够让你少些写这样的一次性业务代码。

在上一节中我们编写了一些实用的代码，实现了在按键时为选择范围内的文本添加 **加粗** 格式。不过这些代码并不仅局限在 **加粗** 文本上，只要我们更改一些变量，它同样能很容易地应用到 _斜体_ 文本或 `代码块` 文本上。

所以让我们将业务逻辑抽取为可复用的插件，来开闭 _任何_ 按键事件触发的 _任何_ mark 类型吧。

从之前的应用开始：

```js
class App extends React.Component {

  state = {
    state: initialState,
    schema: {
      marks: {
        bold: props => <strong>{props.children}</strong>
      }
    }
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  onKeyDown = (event, data, change) => {
    if (!event.metaKey || event.which != 66) return
    event.preventDefault()
    change.toggleMark('bold')
    return true
  }

  render() {
    return (
      <Editor
        schema={this.state.schema}
        state={this.state.state}
        onChange={this.onChange}
        onKeyDown={this.onKeyDown}
      />
    )
  }

}
```

我们来编写一个接收下列参数的新函数：要开闭的 mark 类型 `type` 和按下的按键键值 `code`。

```js
function MarkHotkey(options) {
  // 从用户传入的参数中获取选项值。
  const { type, code, isAltKey = false } = options
}
```

好的，这不难实现。不过这并没有什么实际用处。

要使插件真正工作，我们需要插件函数返回一个【插件对象】给 Slate。Slate 的插件对象实际上只是普通对象，但包含了映射到 `Editor` 中特定回调的属性。

在目前的情况下，我们的插件对象只需要包含一个属性：一个 `onKeyDown` 回调。我们可以从现在的应用代码中复制出其业务逻辑：

```js
function MarkHotkey(options) {
  const { type, code, isAltKey = false } = options

  // 返回我们包含了 `onKeyDown` 回调的插件对象。
  return {
    onKeyDown(event, data, change) {
      // 检查按下的键是否匹配 `code` 选项。
      if (!event.metaKey || event.which != code || event.altKey != isAltKey) return

      // 避免插入默认字符。
      event.preventDefault()

      // 根据 `type` 来开闭 mark。
      change.toggleMark(type)
      return true
    }
  }
}
```

好了！现在我们有了明显进展，代码可以为任何类型的 mark 复用了。

既然我们已经编写了自己的插件，我们就可以将硬编码的逻辑从应用中移除，并将其替换为新的 `MarkHotkey` 插件了。传入同样的参数来保持我们的 **加粗** 功能完整：

```js
function BoldMark(props) {
  return <strong>{props.children}</strong>
}

// 初始化加粗文本插件。
const boldPlugin = MarkHotkey({
  type: 'bold',
  code: 66
})

// 创建插件数组。
const plugins = [
  boldPlugin
]

class App extends React.Component {

  state = {
    state: initialState,
    schema: {
      marks: {
        bold: props => <strong>{props.children}</strong>
      }
    }
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  render() {
    return (
      // 添加 `plugin` 属性到编辑器，并移除 `onKeyDown`。
      <Editor
        plugins={plugins}
        schema={this.state.schema}
        state={this.state.state}
        onChange={this.onChange}
      />
    )
  }

}
```

太棒了。如果你测试了现在的编辑器，你会注意到所有的东西都和之前一样工作，不过我们已经通过插件将逻辑优雅地封装了，这样我们就能够 _非常_ 容易地添加更多的 mark 类型了！

让我们添加 _斜体_，`代码`、~~中划线~~ 和下划线 mark 吧：

```js
// 为每种 mark 初始化一个插件…
const plugins = [
  MarkHotkey({ code: 66, type: 'bold' }),
  MarkHotkey({ code: 67, type: 'code', isAltKey: true }),
  MarkHotkey({ code: 73, type: 'italic' }),
  MarkHotkey({ code: 68, type: 'strikethrough' }),
  MarkHotkey({ code: 85, type: 'underline' })
]

class App extends React.Component {

  state = {
    state: initialState,
    schema: {
      marks: {
        bold: props => <strong>{props.children}</strong>,
        // 添加新的 mark renderer…
        code: props => <code>{props.children}</code>,
        italic: props => <em>{props.children}</em>,
        strikethrough: props => <del>{props.children}</del>,
        underline: props => <u>{props.children}</u>,
      }
    }
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  render() {
    return (
      <Editor
        plugins={plugins}
        schema={this.state.schema}
        state={this.state.state}
        onChange={this.onChange}
      />
    )
  }

}
```

这就是了！我们只做了一点微小的工作就为编辑器实现了一堆新特性！并且，我们能够将快捷键逻辑抽离并测试，使得代码更容易维护。

其实，除非你对键值有特殊的了解，你可能都不知道我们当前的快捷键是什么。

我们来解决这个问题吧。

npm 上的 `keycode` 模块使得我们能够非常简单地解决这个问题。

首先安装它：

```
npm install keycode
```

然后我们将其添加到插件内：

```js
// 导入 keycode 模块。
import keycode from `keycode`

function MarkHotkey(options) {
  // 将选项转换为接受一个 `key`。
  const { type, key, isAltKey = false } = options

  return {
    onKeyDown(event, data, change) {
      // 改变比较方式，使用按键名称进行比较。
      if (!event.metaKey || keycode(event.which) != key || event.altKey != isAltKey) return
      event.preventDefault()
      change.toggleMark(type)
      return true
    }
  }
}
```

这样我们就能够让代码对后来者而言易读得多了：

```js
// 使用清晰得多的按键名称而不是按键 code 值！
const plugins = [
  MarkHotkey({ key: 'b', type: 'bold' }),
  MarkHotkey({ key: 'c', type: 'code', isAltKey: true }),
  MarkHotkey({ key: 'i', type: 'italic' }),
  MarkHotkey({ key: 'd', type: 'strikethrough' }),
  MarkHotkey({ key: 'u', type: 'underline' })
]

class App extends React.Component {

  state = {
    state: initialState,
    schema: {
      marks: {
        bold: props => <strong>{props.children}</strong>,
        code: props => <code>{props.children}</code>,
        italic: props => <em>{props.children}</em>,
        strikethrough: props => <del>{props.children}</del>,
        underline: props => <u>{props.children}</u>,
      }
    }
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  render() {
    return (
      <Editor
        plugins={plugins}
        schema={this.state.schema}
        state={this.state.state}
        onChange={this.onChange}
      />
    )
  }

}
```

这就是插件的强大之处了。插件在增强代码表达能力的同时也使得代码更精简。并且，由于 Slate 是将插件作为一等公民设计的，使用它们也是非常简单的！

<br/>
<p align="center"><strong>Next:</strong><br/><a href="./saving-to-a-database.md">保存到数据库</a></p>
<br/>
