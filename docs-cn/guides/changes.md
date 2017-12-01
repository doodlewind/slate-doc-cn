# Changes

所有对 Slate 编辑器 value 的变更，不论是 `selection`、`document` 还是 `history` 等，都是通过 "changes" 发生的——具体地说，是通过 [`Change`](../reference/slate/change.md) 模型。

这很重要，因为 `Change` 模型负责确保全部对 Slate value 的变更都能够表达为底层的 [operations](../reference/slate/operation.md)。不过这不需要你来操行，因为这是自动处理的。

你只需要理解 change 的概念…


## 关键在于表达力

Slate 中的 Change 的设计是最优先考虑表达能力的。

假设你在构建一个强大的编辑器，它会逐渐变得复杂，而你将会需要编写代码来执行各种不同类型的编程式变更。比如，你会需要移除节点、插入段落、移动选择范围等。

而如果 change 的 API 显得冗长，或需要不同步骤中大量的操作才能执行，你的代码会很快膨胀到难以理解。
为了解决这个问题，Slate 具备表达力很强且支持链式调用的 change。像这样：

```js
change
  .focus()
  .selectAll()
  .delete()
  .insertText('A bit of rich text, followed by...')
  .moveToOffsets(10, 14)
  .addMark('bold')
  .collapseToEndOfBlock()
  .insertBlock({ type: 'image', isVoid: true })
  .insertBlock('paragraph')
```

希望阅读这段代码能够让你分辨出这些 change 能够使得…全部文档内容被选中并删除、插入一些文本、加粗一个词、最后加入一个图片块和一个段落块。

当然了一般情况下你不需要链式调用这么多东西。

关键在于，在这短短几行代码之间你具有了充分的表达能力。

这样，当你阅读触发了什么行为的时候，你可以更容易地理解代码，不再需要坐下来尝试解析一堆中间变量来获知你到底想要实现什么了。


## Change 分类

Slate 默认提供了不少不同种类的 change，理解它们可能可以帮助你理解在编写编辑器逻辑时需要调用什么方法…

### 面向特定 Range

这些形如 `deleteAtRange()`、`addMarkAtArange()` 和 `unwrapBlockAtRange()` 等的 change 接收一个 [`Range`](./range.md) 参数，并应用到该 range 内的全部节点上。它们使用得并不频繁，因为你可能会使用下一种类型的 change 而不是这种…

### 面向当前选择范围

这些形如 `delete()`、`addMark()` 和 `insertBlock()` 等的 change 和 `*AtRange` 等价，但不需要传入 range 参数，因为它们是应用编辑刀座到用户的当前选择范围上的。在你需要编程式地【像用户那样】执行编辑操作时，它们很有用。

### 面向 Selection

这些形如 `blur()`、`collapseToStart()` 和 `moveToRangeOf()` 等的 change 会改变 `value.selection` 模型并更新用户光标，但不影响文档内容。

### 面向特定 Node

这些形如 `removeNodeByKey()`、`setNodeByKey()` 和 `removeMarkByKey()` 的 change 会接收一个面向特定节点的 `key` 字符串，并通过多种方式更改节点。在你已经获得了对 `props.node.key` 的引用，需要编程式地从自定义组件内部触发 change 时，你用到的可能就是它们。

### 面向顶层 Value

这些形如 `setData()` 和 `setDecorations` 的 change 会作用于顶层 [`Value`](../reference/slate/value.md) 对象。这些内容更加高级。

### 面向历史记录

这些形如 `undo()` 和 `redo()` 等的 change 会作用于已发生的操作历史。一般来说你不需要担心这些，因为它们已经绑定到了你期望的键盘快键键上，用户可以使用。


## 触发 Change

当你需要更改 Slate value 的时候，你基本上会位于下面四个地方之一…

### 1. 在 Slate 回调内

第一个地方是 `onKeyDown` 或 `onPaste` 这样 Slate 控制的事件回调中。这些事件回调的函数签名是 `event, change, editor`。其中，`change` 参数是可操纵的 [`Change`](../reference/slate/change.md) 对象。比如…

```js
function onKeyDown(event, change, editor) {
  if (event.key == 'Enter') {
    change.splitBlock()
  }
}
```

所有你调用的 change 方法都会应用，当事件回调结束解析时，编辑器将自动更新这些 change。

### 2. 从自定义节点组件内

第二个地方是自定义节点组件内部。比如，你可能有个 `<Image>` 组件，而你想要在点击图片时触发一个 change。

在这种情形下，你需要使用经由 `props.editor` 获取到 Slate [`<Editor>`](../reference/slate-react/editor.md) 下的 `change()` 方法。比如…

```js
class Image extends React.Component {

  onClick = (event) => {
    this.props.editor.change((change) => {
      change.removeNodeByKey(this.props.node.key)
    })
  }

  render() {
    <img
      {...this.props.attributes}
      onClick={this.onClick}
    />
  }

}
```

这里的 `editor.change()` 方法会基于编辑器的当前 value，为你创建新的 [`Change`](../reference/slate/change.md) 对象。然后你可以调用任意你所需要的 change 方法，新 value 将应用到编辑器中。

### 3. 从 Schema 规则内

第三个你可能需要触发 change 操作的地方——为了处理更复杂的使用场景——是在编辑器 [`Schema`](../references/slate/schema.md) 内的自定义校验规则中。比如…

```js
{
  blocks: {
    list: {
      nodes: [{ types: ['item'] }],
      normalize: (change, reason, context) => {
        if (reason == 'child_type_invalid') {
          change.wrapBlockByKey(context.child.key, 'item')
        }
      }
    }
  }
}
```

当一条规则校验失败时，Slate 传递一个 [`Change`](../reference/slate/change.md) 对象到规则可能对应的 `normalize` 函数中。你能够使用这个对象来应用所需的 change 来使得文档能够通过下一次的校验。

### 4. 从 Slate 外部

这是第四个触发 change 的地方，也是最危险的一个。你需要知道任何从 Slate 编辑器外部触发的 change 可能不会被插件识别、可能用奇怪的方式影响历史记录。并且可能对于协同编辑的实现不可用。

这就是说，如果你觉得没问题，你可以通过对 Slate [`Value`](../reference/slate/value.md) 使用 `change()` 方法的方式手动触发 change。比如：

```js
const change = value.change()
  .selectAll()
  .delete()

const newValue = change.value
```

注意你需要通过直接访问 `change.value` 属性的方式来获得新的 value。


## 复用 Change

在使用这些内置的 change 基础上，在你的编辑器复杂度增长过程中，你可能需要编写你自己的 change 来复用。这种方式下，你可以复用一个单独的 `insertImage` change 而不是一直重复编写 `insertBlock(...args)`。

要实现这一点，你需要按照 Slate 核心的约定那样以接收 `(change, ...args)` 的参数格式定义 change 函数。这里 `change` 是当前的可变 `change` 对象，而 `...args` 是任何其它需要接受来执行 change 的内容。

比如，这是两个简单的插入 block 类 change…

```js
function insertParagraph(change) {
  change.insertBlock('paragraph')
}

function insertImage(change, src) {
  change.insertBlock({
    type: 'image',
    isVoid: true,
    data: { src },
  })
}
```

注意，如果将插入图片的逻辑重写多次而不是封装在一个函数中，那很会就会变得乏味。在现在定义了 change 函数后，你就可以重用它们了！

不过杯具的是你不能直接链式调用这些函数，因为 `change` 对象实际上并不知道它们。实际上，你需要使用 `.call()` 方法：

```js
change
  .call(insertParagraph)
  .call(insertImage, 'https://google.com/logo')
```

你不仅能够通过 `.call()` 来使用它们，还可以在编写对 `editor` 的一次性 change 时，通过 `editor.change()`来使用。比如：

```js
editor.change(insertImage, 'https://google.com/logo')
```

这就是把函数签名标准化的好处了！
