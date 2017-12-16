
# 数据模型

Slate 是基于一个与 DOM 高度相似的数据模型的。在开始使用 Slate 时，最重要的地方之一就是让你自己熟悉这个熟悉模型。这篇指南将帮助你做到这点！


## 对 DOM 的借鉴

Slate 的主要原则之一就是它尝试尽量多地借鉴原生 DOM 的 API。

如果你考虑一下，这就很有意义。Slate 挺像一个更好的 `contenteditable` 实现，它自身是基于 DOM 构建的。而人们总是使用 DOM 来表达类似富文本类型的结构。对 DOM 的借鉴有助于让类库对新用户更加友好，并让我们能够复用久经考验的模式而无需重新自己重新发明轮子。

因为借鉴了 DOM，所以 Slate 的数据模型支持了 [`Document`](../reference/slate/document.md) 下 [`Block`](../reference/slate/block.md)、[`Inline`](../reference/slate/inline.md) 和 [`Text`](../reference/slate/text.md) 等格式的节点。你可以用 [`Range`](../reference/slate/range.md) 引用文档的部分。还有一种特殊的 range 称为 "selection"，可以表达用户的当前光标选择范围。


## 不可变对象

Slate 的数据模型是基于 [`Immutable.js`](https://facebook.github.io/immutable-js/) 对象构建的。这允许我们大大优化渲染速度，并确保我们不被底层 bug 困扰。

特别地，Slate 的模型是 [`Immutable.Record`](https://facebook.github.io/immutable-js/docs/#/Record) 对象，获取它们的值的方式和 JavaScript 对象非常接近：

```js
const block = Block.create({ type: 'paragraph' })

block.kind // "block"
block.type // "paragraph"
```

但在更新值时，你需要使用 [`Immutable.Record` API](https://facebook.github.io/immutable-js/docs/#/Record/set)。

Slate 对象的集合表示为不可变的 `Lists`、`Sets`、`Stacks` 等。这意味着对 `filter`、`includes`、`take`、`skip`、`rest`、`last` 等高表达力方法的良好支持。

如果你之前没有用过 Immutable.js，这肯定是有学习曲线的。在你使用 Slate 之前，你可以查阅 [Immutable.js 文档](https://facebook.github.io/immutable-js/docs/#/)。一旦你掌握了它的诀窍，它完全不会拖累你，不过还是可能花费你几天时间，并且在开始时你也可能写出些【不太高效】的代码。


### The "Value"

Slate 中顶级的对象——封装了整个 Slate 编辑器值的对象——叫做 [`Value`](../reference/slate/value.md)。

它是由一个带内容的文档，以及一个表示用户当前光标选择范围的 selection 构成。它还有一个历史属性来追踪变更，以及一些形如 `decorations` 和 `data` 这样更加高阶的属性。

> 📋 对更多信息，请查阅 [`Value` 参考](../reference/slate/value.md)。


## 文档和节点

Slate 文档是嵌套、递归的。这意味着文档具有块级节点，而且这些块级节点可以具有子块级节点——一路往下。这允许你对表格或带标注的数字一类更加复杂的行为建模。

不像 DOM，Slate 会对它的文档添加一些额外的限制，来减少操作它们的复杂性，并防止出现【不可能】的情形。这些限制是：

- **文档只能包含块级节点作为直接子节点。**这个限制借鉴了富文本编辑器的工作方式，而顶层块级元素在按下 <kbd>enter</kbd> 的时候可以被切分开。

- **块级节点只能包含其它块级节点、行内节点或文本节点。**这是另一条【正常】的限制，使得你能在代码里避免很多的 `if` 冗余代码。块级节点要么包含其它的块级节点，或者包含真实的内容。

- **行内节点只能包含行内或文本节点。**这一点也是为了行为的正常性，并减少荣域代码。一旦你进入了【行内】上下文中，你就不能在其中使用块级节点了。

- **行内节点不能不包含文本。**任何文本内容为空字符串（`''`）的行内节点都会被自动移除。当你考虑用户在行内节点内按下删除键时，这是有意义的。当他们删除了最后一个字符的时候，他们会希望这个行内节点被移除。而当没有字符的时候，你也不能把选择范围放在行内节点中了。所以 Slate 自动将它们从文档中移除以简化问题。

- **文本节点不能邻接于其它文本节点。**任意两个邻接的文本节点都会被自动合并为一个。这避免了光标是在一个文本节点末尾还是在另一个文本节点起始的歧义。不过你可以将一个行内节点放置在两个文本节点之间。

- **块级节点和行内节点必须始终至少包含一个文本节点。**这是用于确保用户的光标始终能【进入】节点，并保证能够正常创建 range 来引用它们。

Slate 会自动实施上面的这些限制。当你对文档 [执行变更](./changes.md) 时，Slate 会检测文档是否失效，并在失效时返回一个【正常化】后的值。

> 🙃 有趣的事实：正常化操作实际上是基于 DOM 的 [`Node.normalize()`](https://developer.mozilla.org/en-US/docs/Web/API/Node/normalize) 实现的！

在文档、块级元素和行内元素的基础上，Slate 还引入了一种 DOM 中原生并不存在的类型：[`Mark`](../reference/slate/mark.md)。


## Marks

Mark 是 Slate 把 **加粗**、_斜体_、`代码` 这样的格式化数据关联到文本的方式，甚至包括评论等复杂格式。

虽然你可以基于 inline 或 mark 来更改样式，但 mark 和 inline 的不同之处在于它不会影响文档中的节点结果，它们只是应用到字符上而已。

这使得 mark 容易理解与操作。因为操作 inline 需要编辑文档的格式，你需要考虑比如对已知节点的切分、它们的继承顺序等事情。另一方面 mark 不论文档如何嵌套，都能应用到字符上。只要你能将内容表达为 `Range`，你就可以为它添加 mark。

不过这也和 mark 的渲染方式有着隐式的关系。当渲染 mark 时，字符将分组为文本的『叶子』，每一片都包含了它们所应用的同样的 mark 集合。但你不能保证一个 mark 的集合是怎样排序的。

这其实和 DOM 很相似，比如这是无效的：

```html
<em>t<strong>e</em>x</strong>t
```

因为元素没有恰当地闭合。作为替代，你需要这么写：

```html
<em>t</em><strong><em>e</em>x</strong>t
```

如果你刚好需要添加 `<strike>` 这样其他的重叠部分，你可能需要重新排列结束标签。在 Slate 中渲染 mark 是类似的——你甚至不能保证即便一个词应用了一个 mark，这个 mark 在相邻文本中是否连续，因为这取决于它如何与其它 mark 相重叠。

这些听起来很复杂，不过你只要按照预期的方法使用 mark 与 inline，你就不需要考虑这么多了……

- Mark 代表 **无序的**，字符层面的格式。
- Inline 代表 **相邻的**，在文档中有语义的元素。


## Range 和『选择范围』

就像 DOM 一样，你可以通过 `Range` 来引用文档的一部分。Slate 有一个特殊的 range 叫做 "selection"，它对应用户当前的光标选择范围。

Range 由 "anchor" 和 "focus" 点决定。anchor 是 range 的起始点，而 focus 是其结束点。每一个点都有一个指向对应节点的 "key"，以及一个 "offset" 偏移量。这个结构形如：

```js
const range = Range.create({
  anchorKey: 'node-a',
  anchorOffset: 0,
  focusKey: 'node-b',
  focusOffset: 4,
  isBackward: false,
})
```

这个更可读的 `node-a` 名称只是伪代码，因为 Slate 默认使用自增的数字字符串——`'1', '2', '3', ...` 但重要的是，每个节点都有一个唯一的 `key` 属性，以及一个通过 key 来引用这些节点的 range。

术语 "anchor" 和 "focus" 是从 DOM 中借鉴的，在这里它们的意义仍然保持一致。anchor 是 range 的开始点，而 focus 是其结束点。不过，我们需要小心，文档中的 anchor 点并不总是在 focus 点的 _前面_。就像在 DOM 中那样，这取决于 range 是正向还是反向的。

这是 MDN 的解释：

> 用户可能创建一个从左到右的选择范围（按照文档顺序）或从右到左（与文档顺序相反）。anchor 是用户开始选择范围的位置，而 focus 是用户结束选择范围的位置。如果你通过桌面鼠标创建了一个选择范围，anchor 放置在你按下鼠标的位置，而 focus 则放置在你松开鼠标按钮的位置。anchor 和 focus 不应该与选择范围的开始、结束位置混淆，因为根据选择范围方向的不同，anchor 既能够放置在 focus 之前，也能放在 focus 之后。
> — [`Selection`, MDN](https://developer.mozilla.org/en-US/docs/Web/API/Selection)

为了简化对 range 的处理，它们同样提供了考虑 range 正反向因素后的 "start" 和 "end" 属性。`startKey` 和 `startOffset` 在文档中将始终位于 `endKey` 和 `endOffset` 之前。

还有很重要的一点时 anchor 和 focus 点 **始终指向『最底层的叶子』文本节点**。它们从不指向 block 和 inline 节点，而是它们的文本子节点。这使得对 range 的处理容易了 _很多_。

> 📋 更多相关信息，请参见 [`Range` 文档](../reference/slate/range.md)。
