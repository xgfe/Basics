	by zhoushiqi 2018.08
目录：

* 性能(performance)
	* performance.hints
	* performance.maxEntrypointSize
	* performance.maxAssetSize
	* performance.assetFilter
* Node(配置node环境的打包选项)
	* node
	* node.console
	* node.process
	* node.global
	* node.__filename
	* node.__dirname
	* node.Buffer
	* node.setImmediate
	* 其他 Node.js 核心库
* 统计信息(stats)
* other options
	* amd
	* bail	
	* cache
	* loader
	* parallelism
	* profile
	* recordsPath
	* recordsInputPath	
	* recordsOutputPath

# 性能(performance)

**performance （object）配置如何展示性能提示。（Webpack如何展示对设定文件大小等性能的警告）**

###performance.hints

可选值：false | "error" | "warning"(默认)

	performance: {
	  hints: false
	}	
* fasle 不展示警告或错误提示。
* warning 将展示一条警告，通知你这是体积大的资源。在开发环境，我们推荐这样。
* errror 将展示一条错误，通知你这是体积大的资源。在生产环境构建时，我们推荐使用 hints: "error"，有助于防止把体积巨大的 bundle 部署到生产环境，从而影响网页的性能。

###performance.maxEntrypointSize

值为int型

入口起点表示针对**指定的入口**，对于所有资源，要充分利用初始加载时(initial load time)期间。
此选项根据入口起点的最大体积，控制 webpack 何时生成性能提示。默认值是：250000 (bytes)。
	
	performance: {
	  maxEntrypointSize: 400000
	}
	
###performance.maxAssetSize

值为int型

资源(asset)是从 webpack **生成的任何文件**。
此选项根据单个资源体积，控制 webpack 何时生成性能提示。默认值是：250000 (bytes)。

	performance: {
	  maxAssetSize: 100000
	}
	
###performance.assetFilter
类型为 function
此属性允许 webpack 控制用于计算性能提示的文件。默认函数如下：

	function assetFilter(assetFilename) {
	    return !(/\.map$/.test(assetFilename))
	};
也可以通过传递自己的函数来覆盖此属性：

	module.exports = {
	  //...
	  performance: {
	    assetFilter: function(assetFilename) {
	      return assetFilename.endsWith('.js');
	    }
	  }
	};
	
#Node

通过对以下选项可以配置：是否 polyfill 或 mock 某些 Node.js 全局变量和模块。这可以使最初为 Node.js 环境编写的代码，在其他环境（如浏览器）中运行。

此功能由 webpack 内部的 [NodeStuffPlugin](https://github.com/webpack/webpack/blob/master/lib/NodeStuffPlugin.js) 插件提供。如果 target 是 "web"（默认）或 "webworker"，那么 NodeSourcePlugin 插件也会被激活。

### node

属性为object，其中每个属性都是 Node.js 全局变量或模块的名称，每个 value 是以下其中之一

* true：提供 polyfill。
* "mock"：提供 mock 实现预期接口，但功能很少或没有。
* "empty"：提供空对象。
* false: 什么都不提供。预期获取此对象的代码，可能会因为获取不到此对象，触发 ReferenceError 而崩溃。尝试使用 require('modulename') 导入模块的代码，可能会触发 Cannot find module "modulename" 错误。

**注意**：不是每个 Node 全局变量都支持所有选项。对于不支持的键值组合(property-value combination)(e.g. process: 'empty'），compiler 会抛出错误。更多细节请查看接下来的章节。

	module.exports = {
	  //默认配置
	  node: {
	    console: false,
	    global: true,
	    process: true,
	    __filename: 'mock',
	    __dirname: 'mock',
	    Buffer: true,
	    setImmediate: true
	
		// 更多选项，请查看“其他 Node.js 核心库”
		}	
	};
	
**从 webpack 3.0.0 开始，node 选项可能被设置为 false，以完全关闭 [NodeStuffPlugin](https://github.com/webpack/webpack/blob/master/lib/NodeStuffPlugin.js) 和 NodeSourcePlugin 插件。**

###node.console
可选值： boolean | "mock"

默认值：false

浏览器提供一个 console 对象，具有非常类似 Node.js console 的接口，所以通常不需要 polyfill。

###node.process
可选值： boolean | "mock"

默认值：true

[process.js](https://github.com/webpack/node-libs-browser/blob/master/mock/process.js)

###node.global
可选值： boolean

默认值：true

关于此对象的准确行为，请查看[源码](https://github.com/webpack/webpack/blob/master/buildin/global.js)。my opinion ：js文件判断返回 this或者window。

###node.__filename
可选值： boolean | "mock"

默认值："mock"

* true: input文件的文件名，是关系到 configuration 中的 context 选项。
* false: 常规的 Node.js __filename 行为。在 Node.js 环境中运行时，output文件的文件名。
* "mock": value 填充为 "index.js".

###node.__dirname
可选值：boolean | "mock"

默认值："mock"

* true: input文件的目录名，是关系到 context 选项。
* false: 常规的 Node.js __dirname 行为。在 Node.js 环境中运行时，output文件的目录名。
* "mock": value 填充为 "/"。

__durname __filename 指向问题 ： 打包之后的代码你会发现 __dirname __filename 全部都是 / 所以设置为 false
  
###node.Buffer
可选值：boolean | "mock"

默认值: true

###node.setImmediate
可选值：boolean | "mock" | "empty"

默认值: true

###其他 Node.js 核心库
可选值：boolean | "mock" | "empty"

**只有当 target 是未指定、"web" 或 "webworker" 这三种情况时，此选项才会被激活（通过 NodeSourcePlugin）。**

当 NodeSourcePlugin 插件启用时，则会使用 [node-libs-browser](https://github.com/webpack/node-libs-browser) 来对 Node.js 核心库 polyfill。请查看 [Node.js 核心库及其 polyfills](https://github.com/webpack/node-libs-browser#readme) 列表。

默认情况下，如果有一个已知的 polyfill，webpack 会对每个 library 进行 polyfill，如果没有，则 webpack 不会执行任何操作。在后一种情况下，如果模块名称配置为 false 值，webpack 表现为不会执行任何操作。

示例：

	module.exports = {
	  //...
	  node: {
	    dns: 'mock',
	    fs: 'empty',
	    path: true,
	    url: false
	  }
	};
	
#统计信息(stats)
只是想要获取某部分 bundle 的信息相比于quiet 和 noInfo 使用 stats 选项是比较好的折衷方式。

**对于 webpack-dev-server，这个属性要放在 devServer 对象里。**

**在使用 Node.js API 时，此选项无效。**

###stats
快捷使用：

	stats: "errors-only"

| Preset        | Alternative    |  Description  |
| --------   | -----:   | :----: |
| "errors-only"        | none     |   只在发生错误时输出   |
| "minimal"        | none      |   只在发生错误或有新的编译时输出    |
| "none"        | false      |   没有输出    |
| "normal"        | true      |   标准输出    |
| "verbose"        | none      |   全部输出    |

精确控制：（所有选项都是可选的）

	stats: {
	
	  // 未定义选项时，stats 选项的备用值(fallback value)（优先级高于 webpack 本地默认值）
	  all: undefined,
	
	  // 添加资源信息
	  assets: true,
	
	  // 对资源按指定的字段进行排序
	  // 你可以使用 `!field` 来反转排序。
	  assetsSort: "field",
	
	  // 添加构建日期和构建时间信息
	  builtAt: true,
	
	  // 添加缓存（但未构建）模块的信息
	  cached: true,
	
	  // 显示缓存的资源（将其设置为 `false` 则仅显示输出的文件）
	  cachedAssets: true,
	
	  // 添加 children 信息
	  children: true,
	
	  // 添加 chunk 信息（设置为 `false` 能允许较少的冗长输出）
	  chunks: true,
	
	  // 将构建模块信息添加到 chunk 信息
	  chunkModules: true,
	
	  // 添加 chunk 和 chunk merge 来源的信息
	  chunkOrigins: true,
	
	  // 按指定的字段，对 chunk 进行排序
	  // 你可以使用 `!field` 来反转排序。默认是按照 `id` 排序。
	  chunksSort: "field",
	
	  // 用于缩短 request 的上下文目录
	  context: "../src/",
	
	  // `webpack --colors` 等同于
	  colors: false,
	
	  // 显示每个模块到入口起点的距离(distance)
	  depth: false,
	
	  // 通过对应的 bundle 显示入口起点
	  entrypoints: false,
	
	  // 添加 --env information
	  env: false,
	
	  // 添加错误信息
	  errors: true,
	
	  // 添加错误的详细信息（就像解析日志一样）
	  errorDetails: true,
	
	  // 将资源显示在 stats 中的情况排除
	  // 这可以通过 String, RegExp, 获取 assetName 的函数来实现
	  // 并返回一个布尔值或如下所述的数组。
	  excludeAssets: "filter" | /filter/ | (assetName) => ... return true|false |
	    ["filter"] | [/filter/] | [(assetName) => ... return true|false],
	
	  // 将模块显示在 stats 中的情况排除
	  // 这可以通过 String, RegExp, 获取 moduleSource 的函数来实现
	  // 并返回一个布尔值或如下所述的数组。
	  excludeModules: "filter" | /filter/ | (moduleSource) => ... return true|false |
	    ["filter"] | [/filter/] | [(moduleSource) => ... return true|false],
	
	  // 和 excludeModules 相同
	  exclude: "filter" | /filter/ | (moduleSource) => ... return true|false |
	    ["filter"] | [/filter/] | [(moduleSource) => ... return true|false],
	
	  // 添加 compilation 的哈希值
	  hash: true,
	
	  // 设置要显示的模块的最大数量
	  maxModules: 15,
	
	  // 添加构建模块信息
	  modules: true,
	
	  // 按指定的字段，对模块进行排序
	  // 你可以使用 `!field` 来反转排序。默认是按照 `id` 排序。
	  modulesSort: "field",
	
	  // 显示警告/错误的依赖和来源（从 webpack 2.5.0 开始）
	  moduleTrace: true,
	
	  // 当文件大小超过 `performance.maxAssetSize` 时显示性能提示
	  performance: true,
	
	  // 显示模块的导出
	  providedExports: false,
	
	  // 添加 public path 的信息
	  publicPath: true,
	
	  // 添加模块被引入的原因
	  reasons: true,
	
	  // 添加模块的源码
	  source: true,
	
	  // 添加时间信息
	  timings: true,
	
	  // 显示哪个模块导出被用到
	  usedExports: false,
	
	  // 添加 webpack 版本信息
	  version: true,
	
	  // 添加警告
	  warnings: true,
	
	  // 过滤警告显示（从 webpack 2.4.0 开始），
	  // 可以是 String, Regexp, 一个获取 warning 的函数
	  // 并返回一个布尔值或上述组合的数组。第一个匹配到的为胜(First match wins.)。
	  warningsFilter: "filter" | /filter/ | ["filter", /filter/] | (warning) => ... return true|false
	};
	
#其它选项(other options)
webpack 支持的其它选项。

###amd
属性：object
设置 require.amd 或 define.amd 的值：

	amd: {
	  jQuery: true
	}
jQuery 版本在 1.7.0 到 1.9.1，如果 loader 提示它对页面包含的多个版本采取了特殊许可时，才会注册为 AMD 模块。

###bail
属性：boolean

在第一个错误出现时抛出失败结果，而不是容忍它。默认情况下，当使用 HMR 时，webpack 会将在终端以及浏览器控制台中，以红色文字记录这些错误，但仍然继续进行打包。要启用它：
	
	bail：true
迫使 webpack 退出其打包过程

###cache
boolean object

缓存生成的 webpack 模块和 chunk，来改善构建速度。缓存默认在观察模式(watch mode)启用。禁用缓存只需简单传入：

	cache: false
	
如果传递一个对象，webpack 将使用这个对象进行缓存。保持对此对象的引用，将可以在 compiler 调用之间共享同一缓存：

	let SharedCache = {};
	
	module.exports = {
	  //...
	  cache: SharedCache
	};

###loader


在 loader 上下文中暴露自定义值。

###parallelism
限制 并行处理的模块数量。可用于微调性能或获得更可靠的性能分析结果

###profile

捕获一个应用程序"配置文件"，包括统计和提示，然后可以使用 Analyze 分析工具进行详细分析。

###recordsPath

开启这个选项可以生成一个 JSON 文件，其中含有 webpack 的 "records" 记录 - 即「用于存储跨多次构建(across multiple builds)的模块标识符」的数据片段。**可以使用此文件来跟踪在每次构建之间的模块变化**。只要简单的设置一下路径,就可以生成这个 JSON 文件：

	recordsPath: path.join(__dirname, 'records.json')
	
如果你使用了代码分离(code splittnig)这样的复杂配置，records 会特别有用。这些数据用于确保拆分 bundle，以便实现你需要的缓存(caching)行为。

###recordsInputPath

指定读取最后一条记录的文件的名称。这可以用来重命名一个记录文件，可以查看下面的实例：

###recordsOutputPath

	recordsInputPath: path.join(__dirname, 'records.json'),
	recordsOutputPath: path.join(__dirname, 'newRecords.json')





