
<br/>
<p align="center"><strong>Previous:</strong><br/><a href="./adding-event-handlers.md">添加事件回调</a></p>
<br/>

# 定制 Block 节点

在之前的示例中，我们使用了一个 paragraph 段落，不过我们实际上没有告诉 Slate 关于 `paragragph` block 类型的相关信息。我们只是让编辑器使用内置的默认 `<div>` 渲染器来渲染节点而已。

不过我们能做的并不止于此。你可以通过 Slate 定义任何类型的自定义 block 节点，比如引用、代码块、列表项等。

下面我们将展示如何实现这一点。让我们从之前的应用开始吧：

```js
class App extends React.Component {

  state = {
    state: initialState
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  onKeyDown = (event, data, change) => {
    if (event.which != 55 || !event.shiftKey) return

    event.preventDefault()

    change.insertText('and');
    return true
  }

  render() {
    return (
      <Editor
        state={this.state.state}
        onChange={this.onChange}
        onKeyDown={this.onKeyDown}
      />
    )
  }

}
```

现在让我们为编辑器添加【代码块】支持。

这里主要的问题是，代码块并不像一个普通的段落那样渲染，它们需要不同的渲染方式。为了实现这一点，我们需要为 `code` 节点定义一个 "renderer"。

节点（译者注，Node 对象）的 renderer 就是简单的 React 组件，像这样：

```js
// 为代码块定义 React 组件以作为 renderer。
function CodeNode(props) {
  return <pre {...props.attributes}><code>{props.children}</code></pre>
}
```

非常简单。

看到了 `props.attributes` 吗？Slate 将需要在 block 顶层渲染的属性通过这种方式传入，这样你就不用自己重复实现了。注意，你 **必须** 将属性混入组件中。

以及，看到了 `props.children` 吗？Slate 会自动为你渲染好 block 的所有子节点，并通过 `props.children` 的形式让你按照使用其它 React 组件的方式来使用。通过这种方式，你就不需要纠结于如何为节点渲染合适的文字子节点或其它内容了。注意，你 **必须** 将 children 作为组件末层的叶子节点渲染。

现在，我们为 `Editor` 添加这个 renderer：

```js
function CodeNode(props) {
  return <pre {...props.attributes}><code>{props.children}</code></pre>
}

class App extends React.Component {

  state = {
    state: initialState,
    // 为应用 state 添加 "schema" 以传入编辑器
    schema: {
      nodes: {
        code: CodeNode
      }
    }
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  onKeyDown = (event, data, change) => {
    if (event.which != 55 || !event.shiftKey) return

    event.preventDefault()

    change.insertText('and')
    return true
  }

  render() {
    return (
      // 传入 `schema` 属性…
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

可以了，不过我们现在需要让用户以某种方式真正地将一个普通的 block 节点转换为一个代码块。为此，我们在 `onKeyDown` 回调中添加一个 `⌘-Alt-C` 快捷键即可：

```js
function CodeNode(props) {
  return <pre {...props.attributes}><code>{props.children}</code></pre>
}

class App extends React.Component {

  state = {
    state: initialState,
    schema: {
      nodes: {
        code: CodeNode
      }
    }
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  onKeyDown = (event, data, change) => {
    // 按下的不是 "`" + cmd + ctrl 时不返回 change。
    if (event.which != 67 || !event.metaKey || !event.altKey) return

    // 阻止插入 "`" 字符的默认行为。
    event.preventDefault()

    // 否则，将当前选中 block 设置为 "code"。
    change.setBlock('code')
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

现在，当你按下 `⌘-Alt-C` 时，光标所在的 block 就会转换为代码块了！神奇吧！

不过这里我们遗漏了一点。当你再次按下 `⌘-Alt-C` 时，应当将代码块转换回段落。要实现这个特性，我们需要添加一些判断，以根据当前选中的 block 是否为代码块来变换 block 类型：

```js
function CodeNode(props) {
  return <pre {...props.attributes}><code>{props.children}</code></pre>
}

class App extends React.Component {

  state = {
    state: initialState,
    schema: {
      nodes: {
        code: CodeNode
      }
    }
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  onKeyDown = (event, data, change) => {
    if (event.which != 67 || !event.metaKey || !event.altKey) return

    event.preventDefault()

    // 判断当前选中 block 是否为代码块。
    const isCode = change.state.blocks.some(block => block.type == 'code')

    // 根据 `isCode` 设置 block 类型。
    change.setBlock(isCode ? 'paragraph' : 'code')
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

搞定了！现在如果你在代码块中按下 `⌘-Alt-C`，就会将代码块转换回普通段落了！

<br/>
<p align="center"><strong>Next:</strong><br/><a href="./applying-custom-formatting.md">应用自定义格式</a></p>
<br/>
