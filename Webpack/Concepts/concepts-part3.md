	by huopanpan 2018.07
	
### 目录：

* The Manifest
	* Runtime
	* Manifest
* Target
	* 用法
	* 多个Target
	* 资源
* Hot module replacement
	*  应用程序中
	*  编译器中
	*  模块中
	*  HMR Runtime中

##The Manifest
**使用webpack构建的典型应用程序或站点,会有三种主要的代码类型**

1. 你或你的团队编写的源码。
2. 你的源码会依赖的任何第三方的 library 或 "vendor" 代码。
3. webpack 的 runtime 和 manifest，管理所有模块的交互。

这里重点介绍着三部分中的最后部分,**runtime**和**manifest**

###Runtime
这里我们只是简单的介绍一下. runtime连同manifest数据,主要是指:在浏览器运行时，webpack 用来连接模块化的应用程序的所有代码。runtime 包含：在模块交互时，连接模块所需的加载和解析逻辑。包括浏览器中的已加载模块的连接，以及懒加载模块的执行逻辑。

###Manifest
那么，一旦你的应用程序中，形如 index.html 文件、一些 bundle 和各种资源加载到浏览器中，会发生什么？你精心安排的 /src 目录的文件结构现在已经不存在，所以 webpack 如何管理所有模块之间的交互呢？这就是 manifest 数据用途的由来……

当编译器(compiler)开始执行、解析和映射应用程序时，它会保留所有模块的详细要点。这个数据集合称为 "Manifest"，当完成打包并发送到浏览器时，会在运行时通过 Manifest 来解析和加载模块。无论你选择哪种模块语法(ES6,commonJS,AMD)，那些 import 或 require 语句现在都已经转换为 \__webpack _ require__ 方法，此方法指向模块标识符(module identifier)。通过使用 manifest 中的数据，runtime 将能够查询模块标识符，检索出背后对应的模块。

###问题
所以，现在你应该对 webpack 在幕后工作有一点了解。“但是，这对我有什么影响呢？”，你可能会问。答案是大多数情况下没有。runtime 做自己该做的，使用 manifest 来执行其操作，然后，一旦你的应用程序加载到浏览器中，所有内容将展现出魔幻般运行。然而，如果你决定通过使用浏览器缓存来改善项目的性能，理解这一过程将突然变得尤为重要。

通过使用 bundle 计算出内容散列(content hash)作为文件名称，这样在内容或文件修改时，浏览器中将通过新的内容散列指向新的文件，从而使缓存无效。一旦你开始这样做，你会立即注意到一些有趣的行为。即使表面上某些内容没有修改，计算出的哈希还是会改变。这是因为，runtime 和 manifest 的注入在每次构建都会发生变化。

查看管理构建文件指南的 [manifest](https://www.webpackjs.com/guides/output-management#the-manifest) 部分，了解如何提取 manifest，并阅读下面的指南，以了解更多长效缓存错综复杂之处。
###进一步阅读

- [分离 Manifest](https://survivejs.com/webpack/optimizing/separating-manifest/)
- [使用 webpack 提供可预测的长效缓存](https://medium.com/webpack/predictable-long-term-caching-with-webpack-d3eee1d3fa31)
- [缓存](https://www.webpackjs.com/guides/caching)

##Target
**因为服务器和浏览器代码都可以用 JavaScript 编写，所以 webpack 提供了多种构建目标(target)，你可以在你的 webpack 配置中设置。**

###用法
设置 target 属性，只需要在你的 webpack 配置中设置 target 的值。

**webpack.config.js**

```
module.exports = {
  target: 'node'
};
```
在上面例子中，使用 node webpack 会编译为用于「类 Node.js」环境（使用 Node.js 的 require ，而不是使用任意内置模块（如 fs 或 path）来加载 chunk）。
每个target都有各种部署(deployment)/环境(environment)特定的附加项，以支持满足其需求。查看[target 的可用值](https://www.webpackjs.com/configuration/target)。

###多个Target
尽管 webpack 不支持向 target 传入多个字符串，你可以通过打包两份分离的配置来创建同构的库：

**webpack.config.js**

```
var path = require('path');
var serverConfig = {
  target: 'node',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.node.js'
  }
  //…
};

var clientConfig = {
  target: 'web', // <=== 默认是 'web'，可省略
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.js'
  }
  //…
};

module.exports = [ serverConfig, clientConfig ];
```
上面的例子将在你的 dist 文件夹下创建 lib.js 和 lib.node.js 文件。
###资源
从上面的选项可以看出有多个不同的部署_目标_可供选择。下面是一个示例列表，以及你可以参考的资源。

[compare-webpack-target-bundles](https://github.com/TheLarkInn/compare-webpack-target-bundles)：有关「测试和查看」不同的 webpack target 的大量资源。也有大量 bug 报告。

[Boilerplate of Electron-React Application](https://github.com/chentsulin/electron-react-boilerplate)：一个 electron 主进程和渲染进程构建过程的很好的例子。

##Hot module replacement

**模块热替换(HMR - Hot Module Replacement)功能会在应用程序运行过程中替换、添加或删除模块，而无需重新加载整个页面。**主要是通过以下几种方式，来显著加快开发速度：

- 保留在完全重新加载页面时丢失的应用程序状态。
- 只更新变更内容，以节省宝贵的开发时间。
- 调整样式更加快速 - 几乎相当于在浏览器调试器中更改样式。

###这一切是如何运行的
让我们从一些不同的角度观察，以了解 HMR 的工作原理.

####在应用程序中
通过以下步骤，可以做到在应用程序中置换(swap in and out)模块：

1. 应用程序代码要求 HMR runtime 检查更新。
2. HMR runtime（异步）下载更新，然后通知应用程序代码。
3. 应用程序代码要求 HMR runtime 应用更新。
4. HMR runtime（异步）应用更新。

你可以设置 HMR，以使此进程自动触发更新，或者你可以选择要求在用户交互时进行更新。

####在编译器中
除了普通资源，编译器(compiler)需要发出 "update"，以允许更新之前的版本到新的版本。"update" 由两部分组成：

1. 更新后的 [manifest](https://www.webpackjs.com/concepts/manifest)(JSON)
2. 一个或多个更新后的 chunk (JavaScript)

manifest 包括新的编译 hash 和所有的待更新 chunk 目录。每个更新 chunk 都含有对应于此 chunk 的全部更新模块（或一个 flag 用于表明此模块要被移除）的代码。

编译器确保模块 ID 和 chunk ID 在这些构建之间保持一致。通常将这些 ID 存储在内存中（例如，使用 [webpack-dev-server](https://www.webpackjs.com/configuration/dev-server/) 时），但是也可能将它们存储在一个 JSON 文件中。

####在模块中
HMR 是可选功能，只会影响包含 HMR 代码的模块。举个例子，通过 style-loader 为 style 样式追加补丁。 为了运行追加补丁，style-loader 实现了 HMR 接口；当它通过 HMR 接收到更新，它会使用新的样式替换旧的样式。

类似的，当在一个模块中实现了 HMR 接口，你可以描述出当模块被更新后发生了什么。然而在多数情况下，不需要强制在每个模块中写入 HMR 代码。如果一个模块没有 HMR 处理函数，更新就会冒泡。这意味着一个简单的处理函数能够对整个模块树(complete module tree)进行更新。如果在这个模块树中，一个单独的模块被更新，那么整组依赖模块都会被重新加载。

有关 module.hot 接口的详细信息，请查看 [HMR API](https://www.webpackjs.com/api/hot-module-replacement) 页面。

####在HMR Runtime中
这些事情比较有技术性……如果你对其内部不感兴趣，可以随时跳到 [HMR API](https://www.webpackjs.com/api/hot-module-replacement) 页面或 [HMR 指南](https://www.webpackjs.com/guides/hot-module-replacement)。

对于模块系统的 runtime，附加的代码被发送到 parents 和 children 跟踪模块。在管理方面，runtime 支持两个方法 check 和 apply。

check 发送 HTTP 请求来更新 manifest。如果请求失败，说明没有可用更新。如果请求成功，待更新 chunk 会和当前加载过的 chunk 进行比较。对每个加载过的 chunk，会下载相对应的待更新 chunk。当所有待更新 chunk 完成下载，就会准备切换到 ready 状态。

apply 方法将所有被更新模块标记为无效。对于每个无效模块，都需要在模块中有一个更新处理函数，或者在它的父级模块们中有更新处理函数。否则，无效标记冒泡，并也使父级无效。每个冒泡继续直到到达应用程序入口起点，或者到达带有更新处理函数的模块（以最先到达为准）。如果它从入口起点开始冒泡，则此过程失败。

之后，所有无效模块都被（通过 dispose 处理函数）处理和解除加载。然后更新当前 hash，并且调用所有 "accept" 处理函数。runtime 切换回闲置状态，一切照常继续。

###入门
在开发过程中，可以将 HMR 作为 LiveReload 的替代。[webpack-dev-server](https://www.webpackjs.com/configuration/dev-server/) 支持 hot 模式，在试图重新加载整个页面之前，热模式会尝试使用 HMR 来更新。更多细节请查看[模块热更新指南](https://www.webpackjs.com/guides/hot-module-replacement)。

与许多其他功能一样，webpack 的强大之处在于它的可定制化。取决于特定项目需求，会有许多种配置 HMR 的方式。然而，对于多数实现来说，webpack-dev-server 能够配合良好，可以让你快速入门 HMR。

