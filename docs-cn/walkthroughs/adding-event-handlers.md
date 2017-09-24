
<br/>
<p align="center"><strong>Previous:</strong><br/><a href="./installing-slate.md">安装 Slate</a></p>
<br/>

# 添加事件回调

很好，现在你已经安装了 Slate 并将它渲染到页面上了。并且当你输入文本时，相应的变更能够显示出来。不过，编辑一个纯文本的字符串肯定满足不了你。

Slate 的强大之处在于它十分容易定制。和你所习惯的 React 组件一样，Slate 支持传入事件回调（event handlers）以在特定事件触发时执行。你已经看到了 `onChange` 回调可用于存储改变后的编辑器状态，不过让我们试着更进一步吧…

我们将展示如何使用 `onKeyDown` 回调以在用户按键时更改编辑器内容。

让我们从之前实现的应用开始：

```js
class App extends React.Component {

  state = {
    state: initialState
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  render() {
    return (
      <Editor
        state={this.state.state}
        onChange={this.onChange}
      />
    )
  }

}
```

现在我们将添加 `onKeyDown` 回调：

```js
class App extends React.Component {

  state = {
    state: initialState
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  // 定义一个新的回调函数，在按键时记录下按下的 key code。
  onKeyDown = (event, data, change) => {
    console.log(event.which)
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

不错，这样一来每当在编辑器中按键时，你就能看到键位对应的 code 显示在控制台中了。这并没有什么大用处，不过至少我们知道代码跑通了。

现在我们的目标是真正地更改编辑内容。对这个示例而言，我们不妨实现在用户按下 `&` 时向内容中添加 `and` 来满足需求。

我们的 `onKeyDown` 回调大致应该形如：

```js
class App extends React.Component {

  state = {
    state: initialState
  }

  onChange = ({ state }) => {
    this.setState({ state })
  }

  onKeyDown = (event, data, change) => {
    // 若按下的键不是 shift + "7" 则不返回 change。
    if (event.which != 55 || !event.shiftKey) return

    // 阻止插入 "&" 至编辑内容的行为。
    event.preventDefault()

    // 在当前光标位置插入 "and" 字符以更改 state。
    change.insertText('and')
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

在这样修改后，试试输入 `&`，你会发现它自动变成了 `and`！

这个示例向你展示了 Slate 回调操作的基本概念。每一个回调在调用时都会传入 `event` 对象与当前的编辑器 `state`。如果你返回了一个新的 `state`，编辑器就会得到更新。简单吧！

<br/>
<p align="center"><strong>Next:</strong><br/><a href="./defining-custom-block-nodes.md">定制 Block 节点</a></p>
<br/>
