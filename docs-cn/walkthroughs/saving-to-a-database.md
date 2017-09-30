
<br/>
<p align="center"><strong>Previous:</strong><br/><a href="./using-plugins.md">使用插件</a></p>
<br/>

# 保存到数据库

既然现在你已经了解了 Slate 编辑器的基本功能了，现在你关注的可能是如何存储你编辑的内容，这样就可以在返回应用的时候下载了。

在这篇教程中，我们将展示如何控制 Slate 存取编辑内容到数据库。

我们从一个基础编辑器开始：

```js
import { Editor, State } from 'slate'

const initialState = State.fromJSON({
  document: {
    nodes: [
      {
        kind: 'block',
        type: 'paragraph',
        nodes: [
          {
            kind: 'text',
            ranges: [
              {
                text: 'A line of text in a paragraph.'
              }
            ]
          }
        ]
      }
    ]
  }
})

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

这会在页面上渲染出一个基础的 Slate 编辑器，在输入时内容会变动。不过如果你刷新页面，所有内容都会回退到一开始的空白状态！

我们现在需要把你的变更保存下来。在这个示例中，我们只会使用 [Local Storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)，不过这已经足够让你在实际实现数据库存取时有一个基础的概念了。

所以，我们需要在 `onChange` 回调中保存 `state` 状态。不过，`onChange` 中接受的 `state` 参数是一个不可变对象，我们不能够直接保存它。我们需要首先将其序列化为一个我们能够理解的格式，比如 JSON！

```js
const initialState = State.fromJSON({
  document: {
    nodes: [
      {
        kind: 'block',
        type: 'paragraph',
        nodes: [
          {
            kind: 'text',
            ranges: [
              {
                text: 'A line of text in a paragraph.'
              }
            ]
          }
        ]
      }
    ]
  }
})

class App extends React.Component {

  state = {
    state: initialState
  }

  onChange = ({ state }) => {
    // 将 state 保存到 Local Storage。
    const content = JSON.stringify(state.toJSON())
    localStorage.setItem('content', content)

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

现在不管在什么时候编辑页面，只要你查看 Local Storage，你就能看到 `content` 值发生改变。

不过…如果你刷新页面，一切都要从头再来了。这是因为我们需要保证初始状态从同样的 Local Storage 位置中拉取出来，像这样：

```js
// 保证只要 Local Storage 中存在初始状态，则从中拉取之。
const existingState = JSON.parse(localStorage.getItem('content'))
const initialState = State.fromJSON(existingState || {
  document: {
    nodes: [
      {
        kind: 'block',
        type: 'paragraph',
        nodes: [
          {
            kind: 'text',
            ranges: [
              {
                text: 'A line of text in a paragraph.'
              }
            ]
          }
        ]
      }
    ]
  }
})

class App extends React.Component {

  state = {
    state: initialState
  }

  onChange = ({ state }) => {
    const content = JSON.stringify(state.toJSON())
    localStorage.setItem('content', content)

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

现在你就能够在页面刷新后保存变更了！

不过，如果你检查 change 回调，会发现目前 _每次_ 编辑器中的变更都会存储到 Local Storage 中，甚至只有选择范围变更时都会！这是因为 `onChange` 在 _每次_ 变更时都被调用了。对于 Local Storage 来说这倒没什么，不过如果你是将内容通过 HTTP 请求存储到数据库的话，这就会带来许多不必要的请求。你可以通过对比前一状态下 `document` 值的方式来修复这个问题。

```js
const existingState = JSON.parse(localStorage.getItem('content'))
const initialState = State.fromJSON(existingState || {
  document: {
    nodes: [
      {
        kind: 'block',
        type: 'paragraph',
        nodes: [
          {
            kind: 'text',
            ranges: [
              {
                text: 'A line of text in a paragraph.'
              }
            ]
          }
        ]
      }
    ]
  }
})

class App extends React.Component {

  state = {
    state: initialState
  }

  onChange = ({ state }) => {
    // 保存前检查 document 是否改变。
    if (state.document != this.state.state.document) {
      const content = JSON.stringify(state.toJSON())
      localStorage.setItem('content', content)
    }

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

现在你的内容就只有在变更时才会保存了！

搞定—你可以向数据库存储 JSON 了。

不过如果你需要 JSON 以外的格式呢？好吧，你需要以不同的方式序列化 state 了。比如，如果你需要以纯文本而非 JSON 的形式存储内容，你可以通过 Slate 自带的 `Plain` 序列化器来实现。像这样：

```js
// 切换到 Plain 序列化器。
import { Editor, Plain } from 'slate'

const existingState = localStorage.getItem('content')
const initialState = Plain.deserialize(existingState || 'A string of plain text.')

class App extends React.Component {

  state = {
    state: initialState
  }

  onChange = ({ state }) => {
    if (state.document != this.state.state.document) {
      const content = Plain.serialize(state)
      localStorage.setItem('content', content)
    }

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

可以了！现在你保存的内容就是纯文本了。

不过，有时候你可能会需要一些更加定制化的内容，并且也会更复杂…像经典的 HTML。这时候，看看下一篇教程吧…


<br/>
<p align="center"><strong>Next:</strong><br/><a href="./saving-and-loading-html-content.md">存取 HTML 内容</a></p>
<br/>
