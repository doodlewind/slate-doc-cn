
# 安装 Slate

Slate 是以 NPM 模块的方式发布的 monorepo，因此你可以这样安装它：

```
npm install slate slate-react
```

同时，你也需要确保 React 已作为 Slate 的 peer dependencies 正确安装：

```
npm install react react-dom immutable
```

_注意，如果你需要使用 Slate 的预构建版本，你可以在执行 `npm install slate` 后获取打包后的 `dist/slate.js` 文件！查阅[使用构建后源码](./using-the-bundled-source.md)指南以了解更多相关信息。_

在完成安装 Slate 后，你需要导入它。

Slate 开放了一套用于构建编辑器的模块。其中，最重要的是 `Editor` 组件。

```js
// 导入 Slate Editor
import { Editor } from 'slate-react'
```

在渲染编辑器的基础上，你还需要给 Slate 一个【初始状态】以作为内容渲染。我们将使用 Slate 附带的 `State` 模型来创建一个仅包含单个段落下若干文本的全新初始状态：

```js
// 导入 `State` 模型。
import { Editor } from 'slate-react'
import { State } from 'slate'

// 构建初始状态…
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
```

既然我们已经获得了初始状态，我们就可以定义我们的 `App` 并将其传入 Slate 的 `Editor` 组件了。像这样：

```js
// 导入 React！
import React from 'react'
import { Editor } from 'slate-react'
import { State } from 'slate'

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

// 定义我们的应用…
class App extends React.Component {

  // 设置应用创建时的初始状态。
  state = {
    state: initialState
  }

  // 发生变更时，使用新的编辑器状态更新应用的 React 状态。
  onChange = ({ state }) => {
    this.setState({ state })
  }

  // 渲染编辑器。
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

你会发现传入 `Editor` 组件的 `onChange` 回调更新了应用的状态。这样，当编辑器重绘时，新的状态就会通过你的 change 反映出来了。

就是这样！

这是 Slate 最基础的示例了。如果你将上述示例渲染到页面上，你应该会看到一个写着 `A line of text in a paragraph.` 文本的段落。当你输入时，文本应当就能变更了！

<br/>
<p align="center"><strong>Next:</strong><br/><a href="./adding-event-handlers.md">添加事件回调</a></p>
<br/>

