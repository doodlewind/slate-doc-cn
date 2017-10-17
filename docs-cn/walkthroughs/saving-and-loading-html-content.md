
<br/>
<p align="center"><strong>Previous:</strong><br/><a href="./saving-to-a-database.md">保存到数据库</a></p>
<br/>

# 存取 HTML 内容

在前一篇教程中，我们展示了如何序列化 Slate 编辑器的内容供后续保存使用。那么如果我们想将内容存储为 HTML 呢？这个过程稍微复杂一些，不过这篇教程会告诉你如何实现的：

我们从一个基础的编辑器开始：

```js
import { Editor } from 'slate'

class App extends React.Component {

  state = {
    state: Plain.deserialize('')
  }

  onChange({ state }) {
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

这可以在页面上渲染出一个基础的 Slate 编辑器。

现在…我们需要添加 [`Html`](../reference/serializers/html.md) 序列化器。要实现这一点，我们需要对使用的 schema 有一些了解。在这个例子中，我们会使用这样一个由几个不同部分组成的 schema：

- 一个 `paragraph` block。
- 一个展示代码示例的 `code` block。
- 一个展示引用的 `quote` block。
- 以及 `bold`、`italic` 和 `underline` 格式。

默认情况下，`Html` 序列化器和 Slate 一样不了解 schema 中内容。要解决这个问题，我们需要将一系列的 `rules` 传入它。每条规则都定义了如何序列化与反序列化 Slate 对象。

作为开始，我们来创建一条包含对段落 block `deserialize` 函数的规则。

```js
const rules = [
  // 通过反序列化函数添加我们的第一条规则。
  {
    deserialize(el, next) {
      if (el.tagName.toLowerCase() == 'p') {
        return {
          kind: 'block',
          type: 'paragraph',
          nodes: next(el.childNodes)
        }
      }
    }
  }
]
```

`deserialize` 函数接受的 `el` 参数就是一个 DOM 元素。`next` 参数是用于反序列化任何（一个或多个）传入元素的函数，你可以使用它递归地遍历子节点。

简要介绍一下 `el.tagName` -- 在浏览器环境中，Slate 使用原生的 `DOMParser` 来解析 HTML，这会返回大写的标签名称。在服务端或 node 环境下，我们推荐[使用 parse5](https://docs.slatejs.org/reference/serializers/html.html#parsehtml) 来解析 HTML。不过，parse5 由于一些规范上的细微复杂性，会返回小写的标签名。因此，我们推荐使用对大小写不敏感的标签对比，这样你的代码就不需要根据 parser 实现去做适配了。

好，这就是 `deserialize` 了。现在让我们定义段落规则的 `serialize` 属性吧：

```js
const rules = [
  {
    deserialize(el, next) {
      if (el.tagName.toLowerCase() == 'p') {
        return {
          kind: 'block',
          type: 'paragraph',
          nodes: next(el.childNodes)
        }
      }
    },
    // 为规则添加添加序列化函数…
    serialize(object, children) {
      if (object.kind == 'block' && object.type == 'paragraph') {
        return <p>{children}</p>
      }
    }
  }
]
```

`serialize` 函数应该会让你感到熟悉。它所做的就是获取 [Slate models](../reference/slate) 并将其转换为 React 元素，然后再将其渲染为 HTML 字符串。

`serialize` 函数接受的 `object` 参数可以是一个 [`Node`](../reference/slate/node.md)、一个 [`Mark`](../reference/slate/mark.md) 或一个特殊的不可变 [`String`](../reference/serializers/html.md#ruleserialize) 对象。`children` 参数则是一个描述当前对象嵌套子节点的 React 元素，以便于递归。

好，现在我们的序列化器就可以处理 `paragraph` 节点了。

添加一些我们需要的其它 block 类型：

```js
// 使用词典重构 block 标签来保持代码简洁。
const BLOCK_TAGS = {
  p: 'paragraph',
  blockquote: 'quote',
  pre: 'code'
}

const rules = [
  {
    // 修改 deserialize 来处理更多类型的 block…
    deserialize(el, next) {
      const type = BLOCK_TAGS[el.tagName.toLowerCase()]
      if (!type) return
      return {
        kind: 'block',
        type: type,
        nodes: next(el.childNodes)
      }
    },
    // 修改 serialize 来处理更多类型的 block…
    serialize(object, children) {
      if (object.kind != 'block') return
      switch (object.type) {
        case 'paragraph': return <p>{children}</p>
        case 'quote': return <blockquote>{children}</blockquote>
        case 'code': return <pre><code>{children}</code></pre>
      }
    }
  }
]
```

现在每种 block 类型都能够处理了。

你会发现即便代码 block 嵌套在 `<pre>` 和 `<code>` 元素中，我们也不需要在 `deserialize` 函数中特殊处理这种情形。这是因为 `Html` 序列化器会在未找到匹配的序列化器时自动对 `el.childNodes` 进行递归。这样，树种未知的标签就只会被跳过，而不是将其内容完全忽略。

好，现在我们的序列化器能够处理 block 类型了，不过我们也需要添加 mark。让我们用一条新规则来实现这一点…


```js
const BLOCK_TAGS = {
  blockquote: 'quote',
  p: 'paragraph',
  pre: 'code'
}

// 添加一个用于 mark 标签的新词典。
const MARK_TAGS = {
  em: 'italic',
  strong: 'bold',
  u: 'underline',
}

const rules = [
  {
    deserialize(el, next) {
      const type = BLOCK_TAGS[el.tagName.toLowerCase()]
      if (!type) return
      return {
        kind: 'block',
        type: type,
        nodes: next(el.childNodes)
      }
    },
    serialize(object, children) {
      if (object.kind != 'block') return
      switch (object.type) {
        case 'code': return <pre><code>{children}</code></pre>
        case 'paragraph': return <p>{children}</p>
        case 'quote': return <blockquote>{children}</blockquote>
      }
    }
  },
  // 添加一条处理 mark 的新规则…
  {
    deserialize(el, next) {
      const type = MARK_TAGS[el.tagName.toLowerCase()]
      if (!type) return
      return {
        kind: 'mark',
        type: type,
        nodes: next(el.childNodes)
      }
    },
    serialize(object, children) {
      if (object.kind != 'mark') return
      switch (object.type) {
        case 'bold': return <strong>{children}</strong>
        case 'italic': return <em>{children}</em>
        case 'underline': return <u>{children}</u>
      }
    }
  }
]
```

很棒，这就是我们所需的全部规则了！现在让我们创建一个新的 `Html` 序列化器并传入这些规则：

```js
import { Html } from 'slate'

// 使用我们上面的规则创建一个新的序列化器。
const html = new Html({ rules })
```

最后，既然我们初始化了序列化器，我们就可以更新应用来存取内容了，像这样：

```js
// 从 Local Storage 中导入初始状态或使用默认。
const initialState = (
  localStorage.getItem('content') ||
  '<p></p>'
)

class App extends React.Component {

  state = {
    state: html.deserialize(initialState),
    // 使用我们的 nodes 和 marks 添加 schema…
    schema: {
      nodes: {
        code: props => <pre {...props.attributes}>{props.children}</pre>,
        paragraph: props => <p {...props.attributes}>{props.children}</p>,
        quote: props => <blockquote {...props.attributes}>{props.children}</blockquote>,
      },
      marks: {
        bold: props => <strong>{props.children}</strong>,
        italic: props => <em>{props.children}</em>,
        underline: props => <u>{props.children}</u>,
      }
    }
  }

  onChange = ({ state }) => {
    // 当文档变更时，将序列化后的 HTML 存储到 Local Storage 中。
    if (state.document != this.state.state.document) {
      const string = html.serialize(state)
      localStorage.setItem('content', string)
    }

    this.setState({ state })
  }

  render() {
    return (
      <Editor
        schema={this.state.schema}
        state={this.state.state}
        onChange={this.onChange}
      />
    )
  }

}
```

这就行了！当你修改编辑器内容时，你就能在 Local Storage 中看到更新后的 HTML 了。当你刷新页面时，这些变更也能够一并保留。
