	by liuhaifeng 2018.08
	
目录：

* Output
	* output.auxiliaryComment
	* output.chunkFilename
	* output.chunkLoadTimeout
	* output.crossOriginLoading
	* output.jsonpScriptType
	* output.devtoolFallbackModuleFilenameTemplate
	* output.devtoolLineToLine
	* output.devtoolModuleFilenameTemplate
	* output.devtoolNamespace
	* output.filename
	* output.hashDigest
	* output.hashDigestLength
	* output.hashFunction
	* output.hashSalt
	* output.hotUpdateChunkFilename
	* output.hotUpdateFunction
	* output.hotUpdateMainFilename
	* output.jsonpFunction
	* output.library
	* output.libraryExport
	* output.libraryTarget
	* Expose a Variable
	* Expose Via Object Assignment
	* Module Definition Systems
	* Other Targets
	* output.path
	* output.pathinfo
	* output.publicPath
	* output.sourceMapFilename
	* output.sourcePrefix
	* output.strictModuleExceptionHandling
	* output.umdNamedDefine
* Module
	* module.noParse
	* module.rules
	* Rule
	* Rule Conditions
	* Rule results
	* Nested rules
	* Rule.enforce
	* Rule.exclude
	* Rule.include
	* Rule.issuer
	* Rule.loader
	* Rule.loaders
	* Rule.oneOf
	* Rule.options / Rule.query
	* Rule.parser
	* Rule.resource
	* Rule.resourceQuery
	* Rule.rules
	* Rule.sideEffects
	* Rule.test
	* Rule.use
	* Condition
	* UseEntry
	* Module Contexts


# output、module

##一、output

<h3 id = "auxiliaryComment">output.auxiliaryComment</h3>

`string` `object`

在和 output.library 和 output.libraryTarget 一起使用时，此选项允许用户向导出容器(export wrapper)中插入注释。要为 libraryTarget 每种类型都插入相同的注释，将 auxiliaryComment 设置为一个字符串

```
(function webpackUniversalModuleDefinition(root, factory) {
  // Test Comment
  if(typeof exports === 'object' && typeof module === 'object')
    module.exports = factory(require("lodash"));
  // Test Comment
  else if(typeof define === 'function' && define.amd)
    define(["lodash"], factory);
  // Test Comment
  else if(typeof exports === 'object')
    exports["someLibName"] = factory(require("lodash"));
  // Test Comment
  else
    root["someLibName"] = factory(root["_"]);
})(this, function(__WEBPACK_EXTERNAL_MODULE_1__) {
  // ...
});
```

对于 libraryTarget 每种类型的注释进行更细粒度地控制，请传入一个对象：

```
auxiliaryComment: {
  root: "Root Comment",
  commonjs: "CommonJS Comment",
  commonjs2: "CommonJS2 Comment",
  amd: "AMD Comment"
}
```


<h3 id = "chunkFilename">output.chunkFilename</h3>

`string` `function`

此选项决定了非入口(non-entry) chunk 文件的名称。可取的值查看 [output.filename](#filename) 选项。

注意，这些文件名需要在 runtime 根据 chunk 发送的请求去生成。因此，需要在 webpack runtime 输出 bundle 值时，将 chunk id 的值对应映射到占位符(如 [name] 和 [chunkhash])。这会增加文件大小，并且在任何 chunk 的占位符值修改后，都会使 bundle 失效。

默认使用 [id].js 或从 output.filename 中推断出的值（[name] 会被预先替换为 [id] 或 [id].）。


<h3 id = "chunkLoadTimeout">output.chunkLoadTimeout </h3>

`integer`

chunk 请求到期之前的毫秒数，默认为 120 000。从 webpack 2.6.0 开始支持此选项。


<h3 id = "crossOriginLoading">output.crossOriginLoading </h3>

`boolean` `string`

只用于 target 是 web，使用了通过 script 标签的 JSONP 来按需加载 chunk。

启用 [cross-origin 属性](https://developer.mozilla.org/en/docs/Web/HTML/Element/script#attr-crossorigin) 加载 chunk。以下是可接收的值……

`crossOriginLoading: false` - 禁用跨域加载（默认）

`crossOriginLoading: "anonymous"` - 不带凭据(credential)启用跨域加载

`crossOriginLoading: "use-credentials"` - 带凭据(credential)启用跨域加载 with credentials


<h3 id = "jsonpScriptType">output.jsonpScriptType </h3>

`string`

允许自定义 script 的类型，webpack 会将 script 标签注入到 DOM 中以下载异步 chunk。可以使用以下选项：

"text/javascript"（默认）

"module"：与 ES6 就绪代码一起使用。


<h3 id = "devtoolFallbackModuleFilenameTemplate">output.devtoolFallbackModuleFilenameTemplate </h3>

`string | function(info)`

当上面的模板字符串或函数产生重复时使用的备用内容。

查看 [output.devtoolModuleFilenameTemplate](#devtoolModuleFilenameTemplate)。


<h3 id = "devtoolLineToLine">output.devtoolLineToLine</h3>

`boolean | object`

避免使用此选项，因为它们已废弃，并将很快删除


<h3 id = "devtoolModuleFilenameTemplate">output. devtoolModuleFilenameTemplate </h3>

`string | function(info)`

此选项仅在 「devtool 使用了需要模块名称的选项」时使用。

自定义每个 source map 的 sources 数组中使用的名称。可以通过传递模板字符串(template string)或者函数来完成。例如，当使用 `devtool: 'eval'`，默认值是：
`
devtoolModuleFilenameTemplate: "webpack://[namespace]/[resource-path]?[loaders]"`

模板字符串(template string)中做以下替换（通过 webpack 内部的 ModuleFilenameHelpers）：

|   模板   |   描述   |
| ---- | ---- |
|   [absolute-resource-path]   |   绝对路径文件名   |
| [all-loaders]| 自动和显式的 loader，并且参数取决于第一个 loader 名称 |
|   [hash]   |  模块标识符的 hash    |
|   [id]   |   模块标识符   |
|    [loaders]  |   显式的 loader，并且参数取决于第一个 loader 名称   |
|   [resource]   |  用于解析文件的路径和用于第一个 loader 的任意查询参数    |
|    [resource-path]  |  不带任何查询参数，用于解析文件的路径    |
|  [namespace]    |  模块命名空间。在构建成为一个 library 之后，通常也是 library 名称，否则为空    |


当使用一个函数，同样的选项要通过 info 参数并使用驼峰式(camel-cased)：

```
devtoolModuleFilenameTemplate: info => {
  return webpack:///${info.resourcePath}?${info.loaders}
}
```

如果多个模块产生相同的名称，使用 [output.devtoolFallbackModuleFilenameTemplate](#devtoolFallbackModuleFilenameTemplate) 来代替这些模块。

<h3 id = "devtoolNamespace">output.devtoolNamespace </h3>

`string`

此选项确定 [output.devtoolModuleFilenameTemplate](#devtoolModuleFilenameTemplate) 使用的模块名称空间。未指定时的默认值为：output.library。在加载多个通过 webpack 构建的 library 时，用于防止 sourcemap 中源文件路径冲突。

例如，如果你有两个 library，分别使用命名空间 library1 和 library2，并且都有一个文件 ./src/index.js（可能具有不同内容），它们会将这些文件暴露为 `webpack://library1/./src/index.js` 和 `webpack://library2/./src/index.js`。

<h3 id = "filename">output.filename</h3>

`string` `function`

此选项决定了每个输出 bundle 的名称。这些 bundle 将写入到 output.path 选项指定的目录下。

对于单个入口起点，filename 会是一个静态名称。

`filename: "bundle.js"`

然而，当通过多个入口起点(entry point)、代码拆分(code splitting)或各种插件(plugin)创建多个 bundle，应该使用以下一种替换方式，来赋予每个 bundle 一个唯一的名称……

使用入口名称：

`filename: "[name].bundle.js"`

使用内部 chunk id

`filename: "[id].bundle.js"`

使用每次构建过程中，唯一的 hash 生成

`filename: "[name].[hash].bundle.js"`

使用基于每个 chunk 内容的 hash：

`filename: "[chunkhash].bundle.js"`

请确保已阅读过[指南 - 缓存](https://www.webpackjs.com/guides/caching)的详细信息。这里涉及更多步骤，不仅仅是设置此选项。

注意此选项被称为文件名，但是你还是可以使用像 `"js/[name]/bundle.js"` 这样的文件夹结构。

注意，此选项不会影响那些「按需加载 chunk」的输出文件。对于这些文件，请使用 `output.chunkFilename` 选项来控制输出。通过 loader 创建的文件也不受影响。在这种情况下，你必须尝试 loader 特定的可用选项。

可以使用以下替换模板字符串（通过 webpack 内部的`[TemplatedPathPlugin]`[TemplatedPathPlugin](https://github.com/webpack/webpack/blob/master/lib/TemplatedPathPlugin.js)）：

|   模板   |    描述  |
| ---- | ---- |
|   [hash]   |    模块标识符(module identifier)的 hash  |
|   [chunkhash]   |   chunk 内容的 hash   |
|   [name]   |   模块名称   |
|    [id]  |    模块标识符(module identifier)  |
|   [query]   |    模块的 query，例如，文件名 `?` 后面的字符串  |


`[hash]` 和 `[chunkhash]` 的长度可以使用 `[hash:16]`（默认为20）来指定。或者，通过指定[output.hashDigestLength](#hashDigestLength) 在全局配置长度。

如果将这个选项设为一个函数，函数将返回一个包含上面表格中替换信息的对象。

> 在使用 `ExtractTextWebpackPlugin` 时，可以用 `[contenthash]` 来获取提取文件的 hash（既不是 `[hash]` 也不是 `[chunkhash]`）。


<h3 id = "hashDigest">output.hashDigest</h3>

在生成 hash 时使用的编码方式，默认为 'hex'。支持 Node.js `hash.digest` 的所有编码。

<h3 id = "hashDigestLength">output.hashDigestLength</h3>

散列摘要的前缀长度，默认为 20。

<h3 id = "hashFunction">output.hashFunction</h3>

`string|function`

散列算法，默认为 `'md5'`。支持 Node.JS `crypto.createHash` 的所有功能。从 `4.0.0-alpha2` 开始，`hashFunction` 现在可以是一个返回自定义 hash 的构造函数。出于性能原因，你可以提供一个不加密的哈希函数(non-crypto hash function)。

`hashFunction: require('metrohash').MetroHash64`

确保 hash 函数有可访问的 `update` and `digest` 方法。

<h3 id = "hashSalt">output.hashSalt</h3>

一个可选的加盐值，通过 Node.JS `hash.update` 来更新哈希。

（加Salt方法，就是加点“佐料”,它由系统随机生成的，并且只有系统知道。）

<h3 id = "hotUpdateChunkFilename">output.hotUpdateChunkFilename</h3>

`string` `function`

自定义热更新 chunk 的文件名。可选的值的详细信息，请查看 [output.filename](#filename) 选项。

占位符只能是 `[id]` 和 `[hash]`，默认值是：

`hotUpdateChunkFilename: "[id].[hash].hot-update.js"`

这里没有必要修改它。

<h3 id = "hotUpdateFunction">output.hotUpdateFunction</h3>

`function`

只在 target 是 web 时使用，用于加载热更新(hot update)的 JSONP 函数。

JSONP 函数用于异步加载(async load)热更新(hot-update) chunk。

详细请查看 [output.jsonpFunction](#jsonpFunction)。


<h3 id = "hotUpdateMainFilename">output.hotUpdateMainFilename</h3>

`string` `function`

自定义热更新的主文件名(main filename)。可选的值的详细信息，请查看 [output.filename](filename) 选项

占位符只能是 [hash]，默认值是：

`hotUpdateMainFilename: "[hash].hot-update.json"`

这里没有必要修改它。


<h3 id = "jsonpFunction">output.jsonpFunction</h3>

`string`

只在 `target` 是 web 时使用，用于按需加载(load on-demand) chunk 的 JSONP 函数。

JSONP 函数用于异步加载(async load) chunk，或者拼接多个初始 chunk(CommonsChunkPlugin, AggressiveSplittingPlugin)。

如果在同一网页中使用了多个（来自不同编译过程(compilation)的）webpack runtime，则需要修改此选项。

如果使用了 [output.library](library) 选项，library 名称会自动附加。



<h3 id = "library">output.library</h3>

`string`

`string` 或 `object`（从 webpack 3.1.0 开始；用于 libraryTarget: "umd"）

`output.library` 的值的作用，取决于[output.libraryTarget](libraryTarget) 选项的值；注意，`output.libraryTarget` 的默认选项是 `var`，所以如果使用以下配置选项：

```
output: {
  library: "MyLibrary"
}
```

如果生成的输出文件，是在 HTML 页面中作为一个 script 标签引入，则变量 MyLibrary 将与入口文件的返回值绑定。

> 示例：
> 
```
var path = require("path");
module.exports = {
	// mode: "development || "production",
	entry: {
		alpha: "./alpha",
		beta: "./beta"
	},
	output: {
		path: path.join(__dirname, "dist"),
		filename: "MyLibrary.[name].js",
		library: ["MyLibrary", "[name]"],
		libraryTarget: "umd"
	}
};
```

> 注意，如果将数组作为 entry，那么只会暴露数组中的最后一个模块。如果将对象作为 entry，还可以使用数组语法暴露（具体查看这个示例 for details)）。

> 有关 `output.library` 以及 `output.libraryTarget` 详细信息，请查看[创建 library 指南](https://www.webpackjs.com/guides/author-libraries)。
> 
> **坑**：有些配置必须要设定好环境才能生效，文档中并未提示，比如demo2。


<h3 id = "libraryExport">output.libraryExport</h3>

`string` or `string[]` (since webpack 3.0.0)

> Default: `_entry_return_`

配置通过libraryTarget暴露的公开模块。默认的`_entry_return_`值是namespace或入口文件返回的默认模块。下面的示例演示了使用libraryTarget时此配置的效果：“var”，但可以使用任何的target。

支持以下配置：

- `libraryExport: "default"` - 入口点的默认导出将分配给 `library target`:

	`var MyDefaultModule = _entry_return_.default;`
	
	```
	!function (e, r){}(window,function (e) {
    	//...});
	}
	```
	
	`var webpackNumbers = function (e){}().MyModule;`

- `libraryExport: "MyModule"` - 指定的模块将分配给`library target`:

	`var MyModule = _entry_return_.MyModule;`(demo1)

- `libraryExport: ["MyModule", "MySubModule"]` - 该数组被解读为要分配给target模块的路径：

	`var MySubModule = _entry_return_.MyModule.MySubModule;`

使用上面指定的libraryExport配置生成的库可以这样使用:

```
MyDefaultModule.doSomething();
MyModule.doSomething();
MySubModule.doSomething();
```

<h3 id = "libraryTarget">output.libraryTarget</h3>

`string`

配置如何暴露 library。

如果设置了 library 但没设置 libraryTarget，则 libraryTarget 默认为 var

**假定 `output.library` 的值配置为 `'MyLibrary'`,暴露 library的方式有：**

> 注意，下面的示例代码中的 `_entry_return_` 是入口起点返回的值。在 bundle 本身中，它是从入口起点、由 webpack 生成的函数的输出结果。

#### 暴露为一个变量

这些选项将入口起点的返回值（例如，入口起点的任何导出值），在 bundle 包所引入的位置，赋值给 output.library 提供的变量名。

- `libraryTarget: "var"` - （默认值）当 library 加载完成，入口起点的返回值将分配给一个变量。当使用此选项时，将 output.library 设置为空，会因为没有变量导致无法赋值。
- `libraryTarget: "assign"` - 这将产生一个隐含的全局变量，可能会潜在地重新分配到全局中已存在的值（谨慎使用）。

```
var MyLibrary = _entry_return_;

// 在一个单独的 script……
MyLibrary.doSomething();

MyLibrary = _entry_return_;

```

#### 通过在对象上赋值暴露

这些选项将入口起点的返回值（例如，入口起点的任何导出值）赋值给一个特定对象的属性（此名称由 output.library 定义）下。

如果 output.library 未赋值为一个非空字符串，则默认行为是，将入口起点返回的所有属性都赋值给一个对象（此对象由 output.libraryTarget 特定），通过如下代码片段：

`
(function(e, a) { for(var i in a) e[i] = a[i]; }(${output.libraryTarget}, _entry_return_)
`

- `libraryTarget: "this"` - 入口起点的返回值将分配给 this 的一个属性（此名称由 output.library 定义）下，this 的含义取决于当前上下文：

	```
	this["MyLibrary"] = _entry_return_;

	// 在一个单独的 script……
	this.MyLibrary.doSomething();
	MyLibrary.doSomething(); // 如果 this 是 window

	```

- `libraryTarget: "window"` - 入口起点的返回值将使用 output.library 中定义的值，分配给 window 对象的这个属性下。

	```
	window["MyLibrary"] = _entry_return_;
	window.MyLibrary.doSomething();
	```

- `libraryTarget: "global"` - 入口起点的返回值将使用 output.library 中定义的值，分配给 global 对象的这个属性下。

	```
	global["MyLibrary"] = _entry_return_;
	global.MyLibrary.doSomething();
	```
	
- `libraryTarget: "commonjs"` - 入口起点的返回值将使用 output.library 中定义的值，分配给 exports 对象。这个名称也意味着，模块用于 CommonJS 环境：
	
	```
	exports["MyLibrary"] = _entry_return_;
	require("MyLibrary").doSomething();
	```
	
#### 模块定义系统

这些选项将导致 bundle 带有更完整的模块头部，以确保与各种模块系统的兼容性。根据 output.libraryTarget 选项不同，output.library 选项将具有不同的含义。

- `libraryTarget: "commonjs2"` - 入口起点的返回值将分配给 module.exports 对象。这个名称也意味着模块用于 CommonJS 环境：
	
	```
	module.exports = _entry_return_;
	require("MyLibrary").doSomething();
	```
	> 注意，output.library 会被省略，因此对于此特定的 output.libraryTarget，无需再	设置 output.library 。
	
- `libraryTarget: "amd"` - 将你的 library 暴露为 AMD 模块。

AMD 模块要求入口 chunk（例如使用 `<script>` 标签加载的第一个脚本）通过特定的属性定义，例如 define 和 require，它们通常由 RequireJS 或任何兼容的模块加载器提供。否则，直接加载生成的 AMD bundle 将导致报错，如 define is not defined。

所以，使用以下配置……

```
output: {
  library: "MyLibrary",
  libraryTarget: "amd"
}
```

生成的 output 将会使用 "MyLibrary" 作为模块名定义，即

```
define("MyLibrary", [], function() {
  return _entry_return_; // 此模块返回值，是入口 chunk 返回的值
});
```

可以在 script 标签中，将 bundle 作为一个模块整体引入，并且可以像这样调用 bundle：
`require(['MyLibrary'], function(MyLibrary) {
  // 使用 library 做一些事……
});`

如果 output.library 未定义，将会生成以下内容。

```
define([], function() {
  return _entry_return_; // 此模块返回值，是入口 chunk 返回的值
});
```

如果直接加载 `<script>` 标签，此 bundle 无法按预期运行，或者根本无法正常运行（在 almond loader 中）。只能通过文件的实际路径，在 RequireJS 兼容的异步模块加载器中运行，因此在这种情况下，如果这些设置直接暴露在服务器上，那么 output.path 和 output.filename 对于这个特定的设置可能变得很重要。

- libraryTarget: "umd" - 将你的 library 暴露为所有的模块定义下都可运行的方式。它将在 CommonJS, AMD 环境下运行，或将模块导出到 global 下的变量。了解更多请查看 [UMD 仓库](https://github.com/umdjs/umd)。

在这个例子中，你需要 library 属性来命名你的模块：

```
output: {
  library: "MyLibrary",
  libraryTarget: "umd"
}
```

最终输出如下：

```
(function webpackUniversalModuleDefinition(root, factory) {
  if(typeof exports === 'object' && typeof module === 'object')
    module.exports = factory();
  else if(typeof define === 'function' && define.amd)
    define([], factory);
  else if(typeof exports === 'object')
    exports["MyLibrary"] = factory();
  else
    root["MyLibrary"] = factory();
})(typeof self !== 'undefined' ? self : this, function() {
  return _entry_return_; // 此模块返回值，是入口 chunk 返回的值
});
```

> 注意，省略 library 会导致将入口起点返回的所有属性，直接赋值给 root 对象，就像对象分配章节。例如：

`
output: {
  libraryTarget: "umd"
}
`
输出结果如下：

```
(function webpackUniversalModuleDefinition(root, factory) {
  if(typeof exports === 'object' && typeof module === 'object')
    module.exports = factory();
  else if(typeof define === 'function' && define.amd)
    define([], factory);
  else {
    var a = factory();
    for(var i in a) (typeof exports === 'object' ? exports : root)[i] = a[i];
  }
})(typeof self !== 'undefined' ? self : this, function() {
  return _entry_return_; // 此模块返回值，是入口 chunk 返回的值
});
```

从 webpack 3.1.0 开始，你可以将 library 指定为一个对象，用于给每个 target 起不同的名称：

```
output: {
  library: {
    root: "MyLibrary",
    amd: "my-library",
    commonjs: "my-common-library"
  },
  libraryTarget: "umd"
}
```

#### 其他 Targets

- `libraryTarget: "jsonp"` - 这将把入口起点的返回值，包裹到一个 jsonp 包装容器中

`MyLibrary(_entry_return_);`

你的 library 的依赖将由 externals 配置定义。

externals （外部扩展）配置选项提供了「从输出的 bundle 中排除依赖」的方法。相反，所创建的 bundle 依赖于那些存在于用户环境(consumer's environment)中的依赖。（demo1）

<h3 id = "path">output.path</h3>

`string`

output 目录对应一个**绝对路径**。

`path: path.resolve(__dirname, 'dist/assets')`

注意，`[hash]` 在参数中被替换为编译过程(compilation)的 hash。详细信息请查看[指南 - 缓存](https://www.webpackjs.com/guides/caching)。

<h3 id = "pathinfo">output.pathinfo</h3>

`boolean`

告诉 webpack 在 bundle 中引入「所包含模块信息」的相关注释。此选项默认值是 `false`，并且不应该用于生产环境(production)，但是对阅读开发环境(development)中的生成代码(generated code)极其有用。

`pathinfo: true`

注意，这些注释也会被添加至经过 tree shaking 后生成的 bundle 中。

<h3 id = "publicPath">output.publicPath</h3>

`string` `function`

对于按需加载(on-demand-load)或加载外部资源(external resources)（如图片、文件等）来说，output.publicPath 是很重要的选项。如果指定了一个错误的值，则在加载这些资源时会收到 404 错误。

此选项指定在浏览器中所引用的「当前输出目录对应的公共 URL」。相对 URL(relative URL) 会被相对于 HTML 页面（或 <base> 标签）解析。相对于服务的 URL(Server-relative URL)，相对于协议的 URL(protocol-relative URL) 或绝对 URL(absolute URL) 也可是可能用到的，或者有时必须用到，例如：当将资源托管到 CDN 时。

该选项的值是在 runtime(运行时) 或 loader(载入时) 所创建的每个 URL 的前缀。因此，在多数情况下，此选项的值都会以/结束。

默认值是一个空字符串 ""。

简单规则如下：output.path 中的 URL 以 HTML 页面为基准。

```
path: path.resolve(__dirname, "public/assets"),
publicPath: "https://cdn.example.com/assets/"
```

对于这个配置：

```
publicPath: "/assets/",
chunkFilename: "[id].chunk.js"
```

对于一个 chunk 请求，看起来像这样 /assets/4.chunk.js。

对于一个输出 HTML 的 loader 可能会像这样输出：

`<link href="/assets/spinner.gif" />`

或者在加载 CSS 的一个图片时：

`background-image: url(/assets/spinner.gif);`

webpack-dev-server 也会默认从 `publicPath` 为基准，使用它来决定在哪个目录下启用服务，来访问 webpack 输出的文件。

注意，参数中的 [hash] 将会被替换为编译过程(compilation) 的 hash。详细信息请查看指南 - 缓存。

示例：

```
publicPath: "https://cdn.example.com/assets/", // CDN（总是 HTTPS 协议）
publicPath: "//cdn.example.com/assets/", // CDN (协议相同)
publicPath: "/assets/", // 相对于服务(server-relative)
publicPath: "assets/", // 相对于 HTML 页面
publicPath: "../assets/", // 相对于 HTML 页面
publicPath: "", // 相对于 HTML 页面（目录相同）
```

在编译时(compile time)无法知道输出文件的 publicPath 的情况下，可以留空，然后在入口文件(entry file)处使用[自由变量(free variable)](https://stackoverflow.com/questions/12934929/what-are-free-variables) __webpack_public_path__，以便在运行时(runtime)进行动态设置。

```
<!--(webpack-demo)-->
 __webpack_public_path__ = myRuntimePublicPath

// 应用程序入口的其他部分
```
有关 __webpack_public_path__ 的更多信息，请查看[此讨论](https://github.com/webpack/webpack/issues/2776#issuecomment-233208623)。




<h3 id = "sourceMapFilename">output.sourceMapFilename</h3>

`string`

此选项会向硬盘写入一个输出文件，只在 `devtool` 启用了 SourceMap 选项时才使用。

配置 source map 的命名方式。默认使用 `"[file].map"`。

可以使用 [output-filename](#filename) 中的 [name], [id], [hash] 和 [chunkhash] 替换符号。除此之外，还可以使用以下替换符号。`[file]` 占位符会被替换为原始文件的文件名。我们建议**只使用 [file] 占位符**，因为其他占位符在非 chunk 文件(non-chunk files)生成的 SourceMap 时不起作用。

|   模板   |   描述   |
| 	---- 	|  ----	 |
|   [file]    |  模块文件名称    |
|  [filebase] |  模块 [basename](https://nodejs.org/api/path.html#path_path_basename_path_ext)  |


<h3 id = "sourcePrefix">output.sourcePrefix</h3>

`string`

修改输出 bundle 中每行的前缀。

`sourcePrefix: "\t"`

注意，默认情况下使用空字符串。使用一些缩进会看起来更美观，但是可能导致多行字符串中的问题。

这里没有必要修改它。


<h3 id = "strictModuleExceptionHandling">output.strictModuleExceptionHandling</h3>

`boolean`

如果一个模块是在 `require` 时抛出异常，告诉 webpack 从模块实例缓存(require.cache)中删除这个模块。

出于性能原因，默认为 false。

当设置为 `false` 时，该模块不会从缓存中删除，这将造成仅在第一次 `require` 调用时抛出异常（会导致与 node.js 不兼容）。

例如，设想一下 module.js：

`throw new Error("error");`

将 strictModuleExceptionHandling 设置为 false，只有第一个 require 抛出异常：

```
// with strictModuleExceptionHandling = false
require("module") // <- 抛出
require("module") // <- 不抛出
```

相反，将 `strictModuleExceptionHandling` 设置为 true，这个模块所有的 `require` 都抛出异常：

```
<!--webpack-demo/print.js-->

// with strictModuleExceptionHandling = true
require("module") // <- 抛出
require("module") // <- 仍然抛出
```


<h3 id = "umdNamedDefine">output.umdNamedDefine</h3>

`boolean`

当使用了 `libraryTarget: "umd"`，设置：

`umdNamedDefine: true`

会对 UMD 的构建过程中的 AMD 模块进行命名。否则就使用匿名的 define。(demo1)


##二、module

这些选项决定了如何处理项目中的[不同类型的模块](https://www.webpackjs.com/concepts/modules)。

<h3 id = "noParse">module.noParse</h3>

`RegExp | [RegExp]`

`RegExp | [RegExp] | function`（从 webpack 3.0.0 开始）

防止 webpack 解析那些任何与给定正则表达式相匹配的文件。忽略的文件中不应该含有 `import`, `require`, `define` 的调用，或任何其他导入机制。忽略大型的 library 可以提高构建性能。

```
module.exports = {
  //...
  module: {
    noParse: /jquery|lodash/,

    // since webpack 3.0.0
    noParse: function(content) {
      return /jquery|lodash/.test(content);
    }
  }
};
```

<h3 id = "m-rules">module.rules</h3>

`array`

创建模块时，匹配请求的[规则]()数组。这些规则能够修改模块的创建方式。这些规则能够对模块(module)应用 loader，或者修改解析器(parser)。


<h3 id = "rule">rule</h3>

每个规则可以分为三部分 - 条件(condition)，结果(result)和嵌套规则(nested rule)。

<h3 id = "rule-c">Rule 条件</h3>

条件有两种输入值：

1. resource：请求文件的绝对路径。它已经根据 [resolve 规则](https://www.webpackjs.com/configuration/resolve)解析。

2. issuer: 被请求资源(requested the resource)的模块文件的绝对路径。是导入时的位置。

**例如**: 从 `app.js` 导入 `'./style.css'`，resource 是 `/path/to/style.css` ,issuer 是 `/path/to/app.js`。

在规则中，属性 `test`, `include`, `exclude` 和 `resource` 对 resource 匹配，并且属性 `issuer` 对 issuer 匹配。

当使用多个条件时，所有条件都匹配。

> 小心！resource 是文件的_解析路径，这意味着符号链接的资源是真正的路径，而不是_符号链接位置。在使用工具来符号链接包的时候（如 npm link）比较好记，像 /node_modules/ 等常见条件可能会不小心错过符号链接的文件。注意，可以通过 resolve.symlinks 关闭符号链接解析（以便将资源解析为符号链接路径）。


<h3 id = "rule-r">Rule 结果</h3>

规则结果只在规则条件匹配时使用。

规则有两种输入值：

	1. 应用的 loader：应用在 resource 上的 loader 数组。
	2. Parser 选项：用于为模块创建解析器的选项对象。

这些属性会影响 loader：`loader`, `options`, `use`。

也兼容这些属性：`query`, `loaders`。

`enforce` 属性会影响 loader 种类。不论是普通的，前置的，后置的 loader。

`parser` 属性会影响 parser 选项。


<h3 id = "rule-n">嵌套的 Rule</h3>

可以使用属性 [rules](#R_rules) 和 [oneOf](#R_oneOf) 指定嵌套规则。

这些规则用于在规则条件(rule condition)匹配时进行取值。


<h3 id = "R_enforce">Rule.enforce</h3>

可能的值有：`"pre" | "post"`

指定 loader 种类。没有值表示是普通 loader。

还有一个额外的种类"行内 loader"，loader 被应用在 import/require 行内。

所有 loader 通过 `前置, 行内, 普通, 后置` 排序，并按此顺序使用。

所有普通 loader 可以通过在请求中加上 `!` 前缀来忽略（覆盖）。

所有普通和前置 loader 可以通过在请求中加上 `-!` 前缀来忽略（覆盖）。

所有普通，后置和前置 loader 可以通过在请求中加上 `!!` 前缀来忽略（覆盖）。

不应该使用行内 loader 和 `!` 前缀，因为它们是非标准的。它们可在由 loader 生成的代码中使用。


<h3 id = "R_exclude">Rule.exclude</h3>

`Rule.exclude` 是 `Rule.resource.exclude` 的简写。如果你提供了 `Rule.exclude` 选项，就不能再提供 `Rule.resource`。详细请查看 [Rule.resource](#R_resource) 和 [Condition.exclude](#C_exclude)。


<h3 id = "R_include">Rule.include </h3>

`Rule.include` 是 `Rule.resource.include` 的简写。如果你提供了 `Rule.include` 选项，就不能再提供 `Rule.resource`。详细请查看 [Rule.resource](#R_resource) 和 [Condition.exclude](#C_exclude)。


<h3 id = "R_issuer">Rule.issuer </h3>

一个**条件**，用来与被发布的 request 对应的模块项匹配。在以下示例中，a.js request 的`发布者(issuer)`是 index.js 文件的路径。

index.js:
`import A from './a.js'`

这个选项可以用来将 loader 应用到一个特定模块或一组模块的依赖中。


<h3 id = "R_loader">Rule.loader </h3>

Rule.loader 是 Rule.use: [ { loader } ] 的简写。详细请查看 [Rule.use](#R_use) 和 [UseEntry.loader](#U_loader)。


<h3 id = "R_loaders">Rule.loaders </h3>

由于需要支持 Rule.use，此选项已废弃。
Rule.loaders 是 Rule.use 的别名。详细请查看 Rule.use。


<h3 id = "R_oneOf">Rule.oneOf </h3>

规则数组，当规则匹配时，只使用第一个匹配规则。

```
{
  test: /.css$/,
  oneOf: [
    {
      resourceQuery: /inline/, // foo.css?inline
      use: 'url-loader'
    },
    {
      resourceQuery: /external/, // foo.css?external
      use: 'file-loader'
    }
  ]
}
```


<h3 id = "R_potions_query">Rule.options / Rule.query </h3>

`Rule.options` 和 `Rule.query` 是 `Rule.use: [ { options } ]` 的简写。详细请查看 [Rule.use](#R_use) 和 [UseEntry.options](#U_potions)。

> 由于需要支持 `Rule.options` 和 `UseEntry.options`，`Rule.use`，`Rule.query` 已废弃。

<h3 id = "R_parser">Rule.parser </h3>

解析选项对象。所有应用的解析选项都将合并。

解析器(parser)可以查阅这些选项，并相应地禁用或重新配置。大多数默认插件，会如下解释值：

- 将选项设置为 `false`，将禁用解析器。
- 将选项设置为 `true`，或不修改将其保留为 `undefined`，可以启用解析器。

然而，一些解析器(parser)插件可能不光只接收一个布尔值。例如，内部的 `NodeStuffPlugin` 可以接收一个对象，而不是 `true`，来为特定的规则添加额外的选项。

示例（默认的插件解析器选项）：

```
parser: {
  amd: false, // 禁用 AMD
  commonjs: false, // 禁用 CommonJS
  system: false, // 禁用 SystemJS
  harmony: false, // 禁用 ES2015 Harmony import/export
  requireInclude: false, // 禁用 require.include
  requireEnsure: false, // 禁用 require.ensure
  requireContext: false, // 禁用 require.context
  browserify: false, // 禁用特殊处理的 browserify bundle
  requireJs: false, // 禁用 requirejs.*
  node: false, // 禁用 __dirname, __filename, module, require.extensions, require.main 等。
  node: {...} // 在模块级别(module level)上重新配置 node 层(layer)
}
```


<h3 id = "R_resource">Rule.resource </h3>

**条件**会匹配 resource。既可以提供 `Rule.resource` 选项，也可以使用快捷选项 `Rule.test`，`Rule.exclude` 和 `Rule.include`。在 [Rule 条件](#R_c) 中查看详细。


<h3 id = "R_resourceQuery">Rule.resourceQuery </h3>

一个与资源查询匹配的条件。此选项用于测试请求字符串的查询部分（即从问号开始））。如果您要`import Foo from './foo.css?inline'`，则以下条件将匹配：

```
{
  test: /.css$/,
  resourceQuery: /inline/,
  use: 'url-loader'
}
```

<h3 id = "R_rules">Rule.rules </h3>

[规则](#rule)数组，当规则匹配时使用。


<h3 id = "R_test">Rule.test </h3>

`Rule.test` 是 `Rule.resource.test` 的简写。如果你提供了一个 `Rule.test` 选项，就不能再提供 `Rule.resource`。详细请查看 Rule.resource 和 Condition.test。


<h3 id = "R_use">Rule.use </h3>

应用于模块的 [UseEntries](https://www.webpackjs.com/configuration/module/#useentry) 列表。每个入口(entry)指定使用一个 loader。

传递字符串（如：`use: [ "style-loader" ]`）是 loader 属性的简写方式（如：use: `[ { loader: "style-loader "} ]`）。

可以通过传递多个加载器来链接加载器，这些加载器将从右到左应用（从最后到第一个配置）。

```
module.exports = {
  //...
  module: {
    rules: [
      {
        //...
        use: [====
          'style-loader',
          {
            loader: 'css-loader',
            options: {
              importLoaders: 1
            }
          },
          {
            loader: 'less-loader',
            options: {
              noIeCompat: true
            }
          }
        ]
      }
    ]
  }
};
```

详细请查看 [UseEntry](https://webpack.js.org/configuration/module/#useentry)。



<h3 id = "condition">Condition </h3>

条件可以是这些之一：

- 字符串：匹配输入必须以提供的字符串开始。是的。目录绝对路径或文件绝对路径。
- 正则表达式：test 输入值。
- 函数：调用输入的函数，必须返回一个真值(truthy value)以匹配。
- 条件数组：至少一个匹配条件。
- 对象：匹配所有属性。每个属性都有一个定义行为。

`{ test: Condition }`：匹配特定条件。一般是提供一个正则表达式或正则表达式的数组，但这不是强制的。

`{ include: Condition }`：匹配特定条件。一般是提供一个字符串或者字符串数组，但这不是强制的。

`{ exclude: Condition }`：排除特定条件。一般是提供一个字符串或字符串数组，但这不是强制的。

`{ and: [Condition] }`：必须匹配数组中的所有条件

`{ or: [Condition] }`：匹配数组中任何一个条件

`{ not: [Condition] }`：必须排除这个条件

示例:

```
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.css$/,
        include: [
          path.resolve(__dirname, 'app/styles'),
          path.resolve(__dirname, 'vendor/styles')
        ]
      }
    ]
  }
};
```


<h3 id = "UseEntry">UseEntry </h3>

`object`

必须有一个 `loader` 属性是字符串。它使用 loader 解析选项（resolveLoader），相对于配置中的 context 来解析。
![]()
可以有一个 `options` 属性为字符串或对象。值可以传递到 loader 中，将其理解为 loader 选项。

由于兼容性原因，也可能有 `query` 属性，它是 `options` 属性的别名。使用 `options` 属性替代。

Example:

```
module: {
    rules: [
      {
        loader: 'css-loader',
        options: {
          modules: true
        }
      }
    ]
  }
```

注意，webpack 需要生成资源和所有 `loader` 的独立模块标识，包括选项。它尝试对选项对象使用 `JSON.stringify`。这在 99.9% 的情况下是可以的，但是如果将相同的 loader 应用于相同资源的不同选项，并且选项具有一些带字符的值，则可能不是唯一的。

如果选项对象不被字符化（例如循环 JSON），它也会中断。因此，你可以在选项对象使用 `ident` 属性，作为唯一标识符。


<h3 id = "M-C">Module Contexts(模块上下文) </h3>

避免使用这些选项，因为它们已废弃，并将很快删除。


