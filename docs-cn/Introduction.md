
# 介绍

[Slate](http://slatejs.org) 是一个 _完全_ 可定制的富文本编辑框架。

通过 Slate，你可以构建出类似 [Medium](https://medium.com/)、[Dropbox Paper](https://www.dropbox.com/paper) 或者 [Canvas](https://usecanvas.com/) 这样使用直观、富交互、体验业已成为 Web 应用标杆的编辑器。同时，你也无需担心在代码实现上陷入复杂度的泥潭之中。

Slate 之所以能做到这一点，是因为它的所有逻辑都是通过一系列的插件实现的。这样，你就再也不会被某项特性 _在_ 或 _不在_ 编辑器【核心】边界之内的问题所困扰了。你可以将它理解为在 [React](https://facebook.github.io/react/) 和 [Immutable](https://facebook.github.io/immutable-js/) 基础上，一种可插拔的 `contenteditable` 实现。另外，它的灵感来自于 [Draft.js](https://facebook.github.io/draft-js/)，[Prosemirror](http://prosemirror.net/) 和 [Quill](http://quilljs.com/) 等类库。

_**Slate 目前还处于 beta 状态**。目前它已经可用，但你可能需要通过 pull request 修复若干复杂使用场景下的问题。_

<br/>

### Why?

为什么发明 Slate 呢？好吧…_（注意，这部分内容包含了一些[我](https://github.com/ianstormtaylor)的个人观点！）_

在发明 Slate 之前，我尝试了许多不同的富文本编辑器。我发现虽然它们在编写简单示例时基本没有问题，但一旦想要构建一些类似 [Medium](https://medium.com/)、[Dropbox Paper](https://www.dropbox.com/paper) 或者 [Google Docs](https://www.google.com/docs/about/) 这样的内容，你就会发现一些深层次的问题，比如…

- **编辑器硬编码了文档的结构规范，难以定制。**类似加粗和斜体的结构可以开箱即用，但评论、嵌入内容以及更多的定制性需求呢？

- **对文档的编程式变换非常错综复杂。**用户的编写体验可能不错，但在执行编程式变更时却不必要地复杂，而这对于构建高级的编辑行为至关重要。

- **对 HTML、Markdown 等内容的序列化支持看起来像是事后加上的。**这是一个非常常见的使用场景，但要实现将文档转换为 HTML 或 Markdown 的简单功能都需要编写大量的模板代码。

- **重新学习一个新的视图层效率不高且十分受限。**各种编辑器在重新发明视图层的轮子，而非使用 React 这样已有的技术方案。你必须学习一套带着自有限制和陷阱的新系统。

- **对协同编辑没有预先设计好的支持。**编辑器内部的数据结构使其无法用于实时、协作的编辑场景中，除非重写编辑器。

- **代码仓库是单体的，而非小而可复用的。**许多编辑器没有对外开放本应为开发者所复用的内部工具，以至于不得不重新发明轮子。

- **无法构建复杂而存在嵌套关系的文档。**不少编辑器是围绕简单的【扁平】文档结构设计的，这使得表格、嵌入内容和字幕等内容难以理解，有时甚至无法实现。

当然，并非所有的编辑器都存在以上的问题，但如果你已经尝试过一些编辑器，你可能已经踩过类似的坑了。为了摆脱它们在 API 上的限制来达到你期望的用户体验，你得使用各种奇技淫巧，有些体验甚至是完全无法实现的。

如果你遇到了相似的问题，那么也许你会喜欢上 Slate。让我来介绍一下 Slate 是如何解决这些问题的吧…


<br/>

### 原则

Slate 尝试通过一些原则来解决 "[Why?](#why)" 这一节中的问题：

1. **作为一等公民的插件。** 在 Slate 中最重要的一点是，插件是一等公民（first-class）的实体——甚至连编辑器的核心逻辑都是通过插件实现的。这意味着你能够 _完全地_ 定制编辑体验，构建出对标 Medium 和 Canvas 那样的复杂编辑器，而无需对抗各种类库的预设条件。

2. **精简 Schema 的核心。** Slate 的核心逻辑并不对你所编辑的数据结构做任何假设，这意味着你在需要应对复杂场景时不会被编辑器预置的内容所束缚（译者注：此处 Schema 可理解为类似 XML Schema 的文档结构规范）。

3. **支持嵌套的文档模型。** Slate 所使用的文档模型是一棵嵌套的、递归的树，和 DOM 本身十分接近。这使得构建表格和嵌套引用等能够满足进阶需求的复杂组件成为了可能。当然，你同样可以使用单一的层级关系以保证简单性。

4. **无状态、不可变的数据。** 通过使用 React 和 Immutable.js，我们是基于不可变数据结构，以无状态的方式构建 Slate 编辑器的。这大大降低了理解代码的难度，也节约了大量开发插件的时间。

5. **直观的 changes。** Slate 中的内容是通过 "change" 来编辑的，这是一种被设计为支持高阶使用，且极其符合直觉的概念。这样，我们就能够通过它来尽可能简单地编写插件和自定义功能了。

6. **为协同编辑准备的数据模型。** Slate 使用的数据模型——尤其是由 change 更改文档的方式——在设计时就已考虑到对协同编辑的支持。所以，如果你决定为编辑器添加协作功能，你不需要进行彻底的重构。（当然，这还需要你投入更多的努力！）

7. **明确的【核心】边界划分。** 通过插件优先的架构与精简 Schema 的内核，Slate 对于【核心】和【自定义】有着明确得多的划分，从而保证核心的编辑体验不会为各种边缘情况所困扰。

<br/>

### Demo

[**在线示例**](http://slatejs.org)页中可查看全部示例！


<br/>

### 示例

为了对如何使用 Slate 有个基础的概念，不妨查阅如下的若干示例：

- [**纯文本**](https://github.com/ianstormtaylor/slate/tree/master/examples/plain-text) — 展示了最基础的情形：一个经过美化的 `<textarea>`。
- [**富文本**](https://github.com/ianstormtaylor/slate/tree/master/examples/rich-text) — 展示了一个能满足基本功能性需求的富文本编辑器。
- [**自动 Markdown**](https://github.com/ianstormtaylor/slate/tree/master/examples/markdown-preview) — 展示了如何通过添加关键的事件 handler 来处理类似 Markdown 的快捷键。
- [**链接**](https://github.com/ianstormtaylor/slate/tree/master/examples/links) — 展示了如何将纯文本与其关联的数据 wrap 为 inline 节点。 
- [**图片**](https://github.com/ianstormtaylor/slate/tree/master/examples/images) — 展示了如何使用 void（无文本）节点来添加图片。
- [**悬浮菜单**](https://github.com/ianstormtaylor/slate/tree/master/examples/hovering-menu) — 展示了如何实现一个上下文相关的悬浮菜单。
- [**表格**](https://github.com/ianstormtaylor/slate/tree/master/examples/tables) — 展示了如何通过嵌套 block 节点来渲染更加高级的组件。
- [**粘贴 HTML**](https://github.com/ianstormtaylor/slate/tree/master/examples/paste-html) — 展示了如何使用 HTML 序列化器来支持粘贴入的 HTML 内容。
- [**代码高亮**](https://github.com/ianstormtaylor/slate/tree/master/examples/code-highlighting) — 展示了如何通过装饰器来动态标记文本。

如果你对于展示泛用性的示例有着自己的想法，请提出 pull request 吧！


<br/>

### 插件

Slate 鼓励你编写小而可复用的模块。下面这些公用的插件已经在你的项目中可用！

- [`slate-auto-replace`](https://github.com/ianstormtaylor/slate-auto-replace) 在用户输入时自动替换文本，对【智能】排版非常实用！
- [`slate-collapse-on-escape`](https://github.com/ianstormtaylor/slate-collapse-on-escape) 在 `escape` 键按下时缩起当前 selection。
- [`slate-edit-code`](https://github.com/GitbookIO/slate-edit-code) 支持形如 tab 缩进、enter 键软换行的代码编辑操作。
- [`slate-edit-list`](https://github.com/GitbookIO/slate-edit-list) 支持富交互、嵌套的列表编辑操作。
- [`slate-edit-table`](https://github.com/GitbookIO/slate-edit-table) 支持复杂的表格编辑！
- [`slate-paste-linkify`](https://github.com/ianstormtaylor/slate-paste-linkify) 在由剪贴板粘贴入 URL 时，将选中的文本转换为链接。
- [`slate-prism`](https://github.com/GitbookIO/slate-prism) 使用 [Prism.js](http://prismjs.com/) 支持代码高亮！
- [`slate-soft-break`](https://github.com/ianstormtaylor/slate-soft-break) 在 `enter` 键按下时插入一个软换行。
- [`slate-drop-or-paste-images`](https://github.com/ianstormtaylor/slate-drop-or-paste-images) 实现对用户拖拽或粘贴以插入图片的支持！
- [**在 `npm` 上查看所有插件...**](https://www.npmjs.com/browse/keyword/slate)

<br/>

### 文档

如果这是你第一次使用 Slate，不妨查看 [Getting Started](./walkthroughs/installing-slate.md) 中的实战部分来了解 Slate 的架构和思维模型。在你熟悉之后，还可以查看完整的 [API 参考](./reference/slate-react/editor.md)。

- [**实战**](./walkthroughs/installing-slate.md)
- [**API 参考**](./reference/slate-react/editor.md)
- [**FAQ**](./general/faq.md)
- [**资源**](./general/resources.md)

如果这还不够，你还可以随时阅读添加了大量 Readme 并重度注释的[源码](./src)。


<br/>

### 贡献！

非常欢迎各种形式的贡献！查看[贡献指南](./Contributing.md)来了解更多信息吧！

Slate 使用 [MIT 许可协议](./License.md)。

> 译者注，本文档对应 Slate `v0.24.1` 版本。
