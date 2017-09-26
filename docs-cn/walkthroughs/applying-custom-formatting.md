
<br/>
<p align="center"><strong>Previous:</strong><br/><a href="./defining-custom-block-nodes.md">定制 Block 节点</a></p>
<br/>

# 应用自定义格式

在上一节中我们已经了解了如何定制在不同容器内渲染不同文本的 block 节点。不过，Slate 所支持的不仅仅是 "block" 而已。

在本文中，我们将展示如何添加自定义的格式选项，如 **加粗** 、 _斜体_ 、`代码块` 或 ~~中划线~~ 等。

让我们从之前的应用开始：

```js
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
    const isCode = change.state.blocks.some(block => block.type == 'code')

    change.setBlock(isCode ? 'paragraph' : 'code')
    return true
  }

  render() {
    return (
      <Editor
        state={this.state.state}
        schema={this.state.schema}
        onChange={this.onChange}
        onKeyDown={this.onKeyDown}
      />
    )
  }

}
```

现在，我们可以编辑 `onKeyDown` 回调来让按下 `⌘-B` 时，添加一个 "bold" mark 到当前选中的文本中：

```js
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
    if (!event.metaKey) return

    // 根据按键键值判断操作…
    switch (event.which) {
      // 当按下 "B" 时，为文本添加 "bold" mark。
      case 66: {
        event.preventDefault()
        change.addMark('bold')
        return true
      }
      // 当按下 "`" 时，仍使用已有的代码块操作逻辑。
      case 67: {
        if (!event.altKey) return
        const isCode = change.state.blocks.some(block => block.type == 'code')
        event.preventDefault()
        change.setBlock(isCode ? 'paragraph' : 'code')
        return true
      }
    }
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

可以了，现在我们已经完成了快捷键回调的配置了…不过！如果你这时候碰巧选中了一段文本并按下 `⌘-B` 的话，控制台中会产生一条报错。这时由于你还没有告诉 Slate 如何渲染一个 "bold" mark。

对每种你想要添加到 schema 中的 mark 类型，你都需要在 Slate 中为它指定一个类似节点那样的 "renderer"。现在让我们来定义 `bold` 类型的 mark 吧：

```js
// 定义一个 React 组件来渲染粗体文本。
function BoldMark(props) {
  return <strong>{props.children}</strong>
}
```

够简单吧？

现在，我们来为 Slate 添加 mark 信息吧。
我们需要将 mark 添加到 `schema` 对象中的 `mark` 属性下。
并且，我们可以通过将 `addMark` 更改为 `toggleMark` 的方式来支持对 mark 的开闭。

```js
function BoldMark(props) {
  return <strong>{props.children}</strong>
}

class App extends React.Component {

  state = {
    state: initialState,
    schema: {
      nodes: {
        code: CodeNode
      },
      // 添加 "bold" mark 到 schema 中…
      marks: {
        bold: BoldMark
      }
    }
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  onKeyDown = (event, data, change) => {
    if (!event.metaKey) return

    switch (event.which) {
      case 66: {
        event.preventDefault()
        change.toggleMark('bold')
        return true
      }
      case 67: {
        if (!event.altKey) return
        const isCode = change.state.blocks.some(block => block.type == 'code')
        event.preventDefault()
        state.setBlock(isCode ? 'paragraph' : 'code')
        return true
      }
    }
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

现在，只要你选中了一段文本并按下 `⌘-B`，就能看到它被加粗了！神奇吧！

<br/>
<p align="center"><strong>Next:</strong><br/><a href="./using-plugins.md">使用插件</a></p>
<br/>
