# 如果没有`node_modules`

如果`node_modules`让你觉得痛苦，那么没有它是否能得救？

> 我们对 Yarn 2的面世感到兴奋，它是一次全新的发布，包括大量的更新和改进。除了可用性优化和工作区的改进外，Yarn 2还引入了zero-installs 的概念，它可以让开发者在克隆项目后直接运行项目。但是，Yarn 2也带来一些坏性的变化，这让升级过程并不简单直白。而且它默认是即插即用（PnP）环境，同时不支持 React Native PnP 环境。当然，团队可以选择退出 PnP 环境，或者停留在 Yarn 1。但他们应该意识到，Yarn 1目前已经处于维护模式。 --- thoughtworks 技术雷达 V23 Yarn

[pnpm readme](https://github.com/pnpm/pnpm)

![compare](https://camo.githubusercontent.com/83b108abddef5c40f6afc985fa8214edc92b6f2226a83d577074a720907463c8/68747470733a2f2f706e706d2e696f2f696d672f62656e63686d61726b732f616c6f7474612d66696c65732e737667)

哪一行运行的更快？

```typescript
import _ from 'lodash'
import _ from '../node_modules/lodash/lodash'
```

## pros and cons

1. 零安装，意味着依赖包不需要下载，包管理工具不必对比依赖树，能够极大的减少CI的运行时间
2. 零安装，能规避境外依赖库下载困难的问题，因为无需下载
3. 零安装，所有的依赖包都在git repo里，CI checkout代码的时间会变长（相比1，可忽略不计）
4. 项目init时的构建时间非常的长
5. yarn提供的包载入机制会使得app运行时的冷启动时间会变短
6. 尽管yarn官方号称几乎完全向后兼容，但是兼容性仍是值得怀疑的
7. 没有`node_modules`!

发布于2018年九月，Plug'n'Play是一个针对node.js的创新安装策略。在其他语言的工作经验上（例如PHP[自动加载](https://getcomposer.org/doc/04-schema.md#autoload)），它以几乎完全向后兼容的方式构建于常规的`Commonjs require`之上并表现出来了一些有趣的特性。

## `node modules`的难题

以前的安装方式很简单：当运行`yarn install`时，Yarn就生成一个`node_modules`文件夹。这使得Node能够利用其内建的[模块引用解决算法](https://nodejs.org/api/modules.html#modules_all_together)加载依赖。以这样的方式，Node不必知道包（package）是什么，而仅进行文件路径的推断。“这个文件在这里吗？不在。好吧，让我们看看父级`node_modules`，这里有没有呢？还是没有。好吧...”。Node将持续这个过程直至找到一个合适的。因为一些原因，这非常的低效：

1. `node_modules`常常包含数量庞大的文件。生成它们将占据70%以上`yarn install`所需的时间。即使有预先存在的安装也救不了你，因为包管理器仍然需要区分`node_modules`已存在的内容和它应该包含的内容。

2. 因为生成`node_modules`是一个 I/O 密集型操作，包管理器除了简单的文件复制之外没有太多优化余地——即使它可以在可能的情况下使用硬链接或写时复制，也要在操作磁盘之前扫描文件系统的当前状态。

3. 因为Node没有包的概念，它也不知道文件是否要被访问。你写的代码完全有可能在开发中正常工作了一天，但后来在生产中崩溃了，因为你在package.json中漏掉了一条必要的依赖。

4. 即使在运行时，Node解释器也必须进行大量调用`stat`和`readdir`以确定从何处加载每个所需的文件。这是非常浪费的，也是启动Node应用程序花费如此多时间的部分原因。

5. 最后，`node_modules`文件夹的设计是不切实际的，因为它不允许包管理器正确地删除重复包。即使可以使用一些算法来优化树布局（提升），我们仍然无法优化某些特定的模式---不仅导致磁盘使用率高于需要，而且一些包在内存中被多次实例化。

## 修复`node_modules`

Yarn 已经知道关于你的依赖树的一切——它甚至会帮你把它安装在磁盘上。那么，为什么要由 Node 来查找您的包所在的位置呢？相反，包管理器的工作应该是通知解释器包在磁盘上的位置，并管理包之间甚至包版本之间的任何依赖关系。这就是创建 `Plug'n'Play` 的原因。

在这种安装模式下（从 Yarn 2.0 开始成为默认选项），Yarn 生成单个`.pnp.cjs`文件，而不是`node_modules`这种包含各种包副本的文件夹。该`.pnp.cjs`文件包含各种映射：一种是将包名和版本链接到它们在磁盘上的位置，另一种将包名称和版本链接到它们的依赖项列表。有了这些查找表，Yarn 可以立即告诉 Node 在哪里可以找到它需要访问的任何包，只要它们是依赖树的一部分，并且只要这个文件加载到您的环境中（下一节将详细介绍）。

这种方法有多种好处：

1. 安装现在几乎是即时的。Yarn 只需要生成一个文本文件（而不是可能的数万个）。主要的瓶颈是项目中的依赖数量而不是磁盘性能。

2. 由于减少了 I/O 操作，安装更加稳定可靠。特别是在 Windows 上（批量写入和删除文件可能会触发与 Windows Defender 和类似工具的各种意外交互），I/O 繁重的`node_modules`操作更容易失败。

3. 依赖树的完美优化（又名完美提升）和可预测的包实例化。

4. 生成的.pnp.cjs文件可以作为零安装工作的一部分提交到您的存储库，从而无需首先运行yarn install。

5. 更快的应用程序启动！Node 解析几乎不需要像以前那样遍历文件系统层次结构（很快就完全不需要这样做了！）。

## 初始化即插即用

Yarn 生成一个`.pnp.cjs`文件，Node 需要安装该文件才能知道在哪里可以找到相关包。这种注册通常是透明的：node通过您的一个scripts条目执行的任何直接或间接命令都会自动将该`.pnp.cjs`文件注册为运行时依赖项。对于绝大多数用例，以下内容将按您的预期工作：

```json
{
  "scripts": {
    "start": "node ./server.js",
    "test": "jest"
  }
}
```

对于一些剩余的边界情况，可能需要一个小的设置：

- 如果您需要运行任意 Node 脚本，请使用`yarn node`作为解释器，而不是`node`. 这足以将.`pnp.cjs`文件注册为运行时依赖项。

```shell
yarn node ./server.js
```

- 如果您在自动执行 Node 脚本的系统上运行（例如在Google Cloud Platform上），只需在 init 脚本的顶部请求 PnP 文件并调用其setup函数。

```javascript
require('./.pnp.cjs').setup();
```

yarn node通常所做的就是在`NODE_OPTIONS`环境变量中使用`--require` 选项关联`.pnp.cjs`文件路径。如果您愿意，可以自己轻松应用此操作：

```shell
node -r ./.pnp.cjs ./server.js
NODE_OPTIONS="--require $(pwd)/.pnp.cjs" node ./server.js
```

## 即插即用loose模式

因为这种启发式的hoisting算法不是标准化和可预测的，在严格模式下运行的 PnP 将阻止包引用未明确列出的依赖项；即使其他依赖项也依赖于它。这可能会导致某些软件包出现问题。

为了解决这个问题，Yarn 附带了一个“松散”模式，这将导致 PnP 链接器与`node_modules` hoister串行工作——我们将首先生成在典型node_modules安装中将被提升到顶层的包列表，然后记住这个列表作为我们所说的“后备池”。

> 请注意，因为松散模式直接调用node-modules hoister，所以它遵循与链接器node-modules使用的真实算法完全相同的实现！

在运行时，如果依赖项的任何版本最终出现在回退池中，则仍然允许未列出的依赖项的包访问它们（可以使用pnpFallbackMode调整哪些包确切地被允许依赖回退池）。

请注意，回退池的内容尚未确定。如果依赖树包含同一个包的多个版本，则无法确定哪个版本将被提升到顶层。因此，访问回退池的包仍会生成警告（通过process.emitWarning API）。

此模式提供了strictPnP 链接器和node_modules链接器之间的折衷。

为了启用loose模式，请确保该nodeLinker选项设置为pnp（默认值）并将以下内容添加到您的本地.yarnrc.yml文件中：

```
pnpMode: loose
```

[有关该pnpMode选项的更多信息](https://yarnpkg.com/configuration/yarnrc#pnpMode)

### 注意事项

因为我们对解析错误发出警告（而不是抛出错误），所以应用程序无法捕获它们。这意味着在运行时`try/catch`块中尝试`require`可选对等依赖项的常见模式将会打印`warning`，即使它不应该打印警告。这样可能会引起混淆的警告是唯一的运行时暗示，但可以安全地忽略它。

因此，从 2.1 版开始，PnP loose模式将不再是默认模式（正如我们最初计划的那样）。它将继续作为替代方案得到支持，希望能够简化向默认和推荐工作流的过渡：即插即用strict模式。
