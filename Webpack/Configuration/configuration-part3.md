
	by lihuazhu 2018.08

目录：

* 解析(resolve)
	* resolve
	* resolve.alias
	* resolve.aliasFields
	* resolve.cacheWithContext
	* resolve.descriptionFiles
	* resolve.enforceExtension
	* resolve.enforceModuleExtension
	* resolve.extensions
	* resolve.mainFields
	* resolve.mainFiles
	* resolve.modules
	* resolve.unsafeCache
	* resolve.plugins
	* resolve.symlinks
	* resolve.cachePredicate
	* resolveLoader
	* resolveLoader.moduleExten
* Optimization
	* optimization.minimize
	* optimization.minimizer
	* optimization.splitChunks
	* optimization.runtimeChunk
	* optimization.noEmitOnErrors
	* optimization.namedModules
	* optimization.namedChunks
	* optimization.nodeEnv
	* optimization.mangleWasmImports
	* optimization.removeAvailableModules
	* optimization.removeEmptyChunks
	* optimization.mergeDuplicateChunks
	* optimization.flagIncludedChunks
	* optimization.occurrenceOrder
	* optimization.providedExports
	* optimization.usedExports
	* optimization.concatenateModules
	* optimization.sideEffects

# Resolve & Optimization

## 1.resolve

`object`

配置模块如何解析。例如，当在 ES2015 中调用 `import "lodash"`，`resolve` 选项能够对 webpack 查找 `"lodash"` 的方式去做修改（查看[`模块`](https://webpack.docschina.org/concepts/module-resolution#%E6%A8%A1%E5%9D%97%E8%B7%AF%E5%BE%84)）。


### resolve.alias

`object`

创建 `import` 或 `require` 的别名，来确保模块引入变得更简单。例如，一些位于 `src/` 文件夹下的常用模块：

```
alias: {
  Utilities: path.resolve(__dirname, 'src/utilities/'),
  Templates: path.resolve(__dirname, 'src/templates/')
}
```

现在，替换「在导入时使用相对路径」这种方式，就像这样：

```
import Utility from '../../utilities/utility';
```

你可以这样使用别名：

```
import Utility from 'Utilities/utility';
```

也可以在给定对象的键后的末尾添加 `$`，以表示精准匹配：

```
alias: {
  xyz$: path.resolve(__dirname, 'path/to/file.js')
}
```

这将产生以下结果：

```
import Test1 from 'xyz'; // 精确匹配，所以 path/to/file.js 被解析和导入
import Test2 from 'xyz/file.js'; // 非精确匹配，触发普通解析
```

下面的表格展示了一些其他情况：

| `别名：` | `import "xyz"` | `import "xyz/file.js"` |
| -------- | ---------------- | -------------------------|
| `{}` | `/abc/node_modules/xyz/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "/abs/path/to/file.js" }` | `/abs/path/to/file.js` | error |
| `{ xyz$: "/abs/path/to/file.js" }` | `/abs/path/to/file.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "./dir/file.js" }` | `/abc/dir/file.js` | error |
| `{ xyz$: "./dir/file.js" }` | `/abc/dir/file.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "/some/dir" }` | `/some/dir/index.js` | `/some/dir/file.js` |
| `{ xyz$: "/some/dir" }` | `/some/dir/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "./dir" }` | `/abc/dir/index.js` | `/abc/dir/file.js` |
| `{ xyz: "modu" }` | `/abc/node_modules/modu/index.js` | `/abc/node_modules/modu/file.js` |
| `{ xyz$: "modu" }` | `/abc/node_modules/modu/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "modu/some/file.js" }` | `/abc/node_modules/modu/some/file.js` | error |
| `{ xyz: "modu/dir" }` | `/abc/node_modules/modu/dir/index.js` | `/abc/node_modules/dir/file.js` |
| `{ xyz: "xyz/dir" }` | `/abc/node_modules/xyz/dir/index.js` | `/abc/node_modules/xyz/dir/file.js` |
| `{ xyz$: "xyz/dir" }` | `/abc/node_modules/xyz/dir/index.js` | `/abc/node_modules/xyz/file.js` |

如果在 `package.json` 中定义，`index.js` 可能会被解析为另一个文件。

`/abc/node_modules` 也可能在 `/node_modules` 中解析。


###resolve.aliasFields

`string`

指定一个字段，例如 `browser`，根据[此规范](https://github.com/defunctzombie/package-browser-field-spec)进行解析。默认：

```
aliasFields: ["browser"]
```

PS.[`browser`字段的规范](https://github.com/defunctzombie/package-browser-field-spec)

###resolve.cacheWithContext

`boolean`（从 webpack 3.1.0 开始）

如果启用了不安全缓存，请在缓存键(cache key)中引入 `request.context`。这个选项被 [`enhanced-resolve`](https://github.com/webpack/enhanced-resolve/) 模块考虑在内。从 webpack 3.1.0 开始，在配置了 resolve 或 resolveLoader 插件时，解析缓存(resolve caching)中的上下文(context)会被忽略。这解决了性能衰退的问题。


### resolve.descriptionFiles

`array`

用于描述的 JSON 文件。默认：

```
descriptionFiles: ["package.json"]
```


### resolve.enforceExtension

`boolean`

如果是 `true`，将不允许无扩展名(extension-less)文件。默认如果 `./foo` 有 `.js` 扩展，`require('./foo')` 可以正常运行。但如果启用此选项，只有 `require('./foo.js')` 能够正常工作。默认：

```
enforceExtension: false
```


### resolve.enforceModuleExtension

`boolean`

对模块是否需要使用的扩展（例如 loader）。默认：

``` 
enforceModuleExtension: false
```


### resolve.extensions

`array`

自动解析确定的扩展。默认值为：

``` 
extensions: [".js", ".json"]
```

能够使用户在引入模块时不带扩展：

``` 
import File from '../path/to/file'
```

PS.PS.使用此选项，会**覆盖默认数组**，这就意味着 webpack 将不再尝试使用默认扩展来解析模块。对于使用其扩展导入的模块，例如，`import SomeFile from "./somefile.ext"`，要想正确的解析，一个包含“\*”的字符串必须包含在数组中。


### resolve.mainFields

`array`

当从 npm 包中导入模块时（例如，`import * as D3 from "d3"`），此选项将决定在 `package.json` 中使用哪个字段导入模块。根据 webpack 配置中指定的 [`target`](/concepts/targets) 不同，默认值也会有所不同。

当 `target` 属性设置为 `webworker`, `web` 或者没有指定，默认值为：


```
mainFields: ["browser", "module", "main"]
```

对于其他任意的 target（包括 `node`），默认值为：

```
mainFields: ["module", "main"]
```

例如，[D3](https://d3js.org/) 的 `package.json` 含有这些字段：

```
{
  ...
  main: 'build/d3.Node.js',
  browser: 'build/d3.js',
  module: 'index',
  ...
}
```

这意味着当我们 `import * as D3 from "d3"`，实际从 `browser` 属性解析文件。在这里 `browser` 属性是最优先选择的，因为它是 `mainFields` 的第一项。同时，由 webpack 打包的 Node.js 应用程序默认会从 `module` 字段中解析文件。


### resolve.mainFiles

`array`

解析目录时要使用的文件名。默认：

```
mainFiles: ["index"]
```


### resolve.modules

`array`

告诉 webpack 解析模块时应该搜索的目录。

绝对路径和相对路径都能使用，但是要知道它们之间有一点差异。

通过查看当前目录以及祖先路径（即 `./node_modules`, `../node_modules` 等等），相对路径将类似于 Node 查找 'node_modules' 的方式进行查找。

使用绝对路径，将只在给定目录中搜索。

`resolve.modules` defaults to:

```
modules: ["node_modules"]
```

如果你想要添加一个目录到模块搜索目录，此目录优先于 `node_modules/` 搜索：

```
modules: [path.resolve(__dirname, "src"), "node_modules"]
```


### resolve.unsafeCache

`regex` `array` `boolean`

启用，会主动缓存模块，但并**不安全**。传递 `true` 将缓存一切。默认：

```
unsafeCache: true
```

正则表达式，或正则表达式数组，可以用于匹配文件路径或只缓存某些模块。例如，只缓存 utilities 模块：

```
unsafeCache: /src\/utilities/
```

PS.修改缓存路径可能在极少数情况下导致失败。


### resolve.plugins

应该使用的额外的解析插件列表。它允许插件，如 [`DirectoryNamedWebpackPlugin`](https://www.npmjs.com/package/directory-named-webpack-plugin)。

``` 
plugins: [
  new DirectoryNamedWebpackPlugin()
]
```


### resolve.symlinks

`boolean`

是否将符号链接(symlink)解析到它们的符号链接位置(symlink location)。默认：

启用时，符号链接(symlink)的资源，将解析为其_真实_路径，而不是其符号链接(symlink)位置。注意，当使用符号链接 package 包工具时（如 `npm link`），可能会导致模块解析失败。

`resolve.symlinks` 默认值为：

```
symlinks: true
```


### resolve.cachePredicate

`function`

决定请求是否应该被缓存的函数。函数传入一个带有 `path` 和 `request` 属性的对象。默认：

```
cachePredicate: function() { return true }
```


## resolveLoader

`object`

这组选项与上面的 `resolve` 对象的属性集合相同，但仅用于解析 webpack 的 [loader](/concepts/loaders) 包。默认：

```
{
  modules: [ 'node_modules' ],
  extensions: [ '.js', '.json' ],
  mainFields: [ 'loader', 'main' ]
}
```

T> 注意，这里你可以使用别名，并且其他特性类似于 resolve 对象。例如，`{ txt: 'raw-loader' }` 会使用 `raw-loader` 去 shim(填充) `txt!templates/demo.txt`。


## resolveLoader.moduleExtensions

`array`

解析 loader 时，用到扩展名(extensions)/后缀(suffixes)。从 webpack 2 开始，我们[强烈建议](/guides/migrating#automatic-loader-module-name-extension-removed)使用全名，例如 `example-loader`，以尽可能清晰。然而，如果你确实想省略 `-loader`，也就是说只使用 `example`，则可以使用此选项来实现：

```
moduleExtensions: [ '-loader' ]
```


##2.optimization

`object`

虽然webpack 4会根据使用者设置的模式而选择对应的优化策略，但开发者仍然可以手动设置优化策略或者覆盖原有的优化策略。

### optimization.minimize

`boolean`

webpack在这一设置的影响下，会使用UglifyjsWebpackPlugin去缩小打包的bundle
在生产环境下，该设置默认为true。


```
module.exports = {
  //...
  optimization: {
    minimize: false
  }
};
```

### optimization.minimizer


`UglifyjsWebpackPlugin` | `[UglifyjsWebpackPlugin]`

这一设置允许开发者利用一个定制化的UglifyjsWebpackPlugin实例，去覆盖默认的minimizer。

```
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
  //...
  optimization: {
    minimizer: [
      new UglifyJsPlugin({ /* your config */ })
    ]
  }
};

```
PS.相关配置项可见[UglifyjsWebpackPlugin](https://github.com/webpack-contrib/uglifyjs-webpack-plugin)

### optimization.splitChunks

`object`

Webpack 4为动态引入的模块提供了一个开箱即用的通用chunk策略。

相关配置项可以在[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)页面看到。


### optimization.runtimeChunks

`object` `string` `boolean`

把该配置项的值设置为'true'或者'mutiple'，将会为webpack runtime创建一个独立的代码块，添加到入口文件中。

```
module.exports = {
  //...
  optimization: {
    runtimeChunk: {
      name: entrypoint => `runtime~${entrypoint.name}`
    }
  }
};
```
当把配置项的值配置为'single'时，会创建一个供所有代码块公用的运行时文件，该配置项的值等同于：

```
module.exports = {
  //...
  optimization: {
    runtimeChunk: {
      name: 'runtime'
    }
  }
};

```

### optimization.onEmitOnErrors

`boolean`

通过配置该配置项，当发生编译错误的时候，开发者可以跳过发生编译错误的静态资源。这一设置保证了没有错误编译的静态资源被打包。在这种情况下，所有静态资源的`emitted`标记位的值都为`false`。

```
module.exports = {
  //...
  optimization: {
    noEmitOnErrors: true
  }
};
```

### optimization.nameModules

`boolean: false`

当该配置项的值被设置为true，webpack会使用易读性高的模块标识名去为debug提供便利。当该配置项没有被配置时，webpack会在开发模式下把这个配置项设为true，在生产模式下把该配置项设为false。

```
module.exports = {
  //...
  optimization: {
    namedModules: true
  }
};

```

### optimization.nameChunks

`boolean: false`

当该配置项的值被设置为true，webpack会使用易读性高的代码块标识名去为debug提供便利。当该配置项没有被配置时，webpack会在开发模式下把这个配置项设为true，在生产模式下把该配置项设为false。

```
module.exports = {
  //...
  optimization: {
    namedChunks: true
  }
};
```

### optimization.nodeEnv

`string` `bool:false`

在该配置项的影响下，webpack会用给定的配置值来设置`process.env.NODE_ENV`。
除了被设为`false`的情况，该配置项都会使用[DefinePlugin](https://webpack.js.org/plugins/define-plugin/)。如果[mode](https://webpack.js.org/concepts/mode/)被设置对应的值，`optimization.nodeEnv`会与该值保持一致，否则`optimization.nodeEnv`的值应为'production'。


```
module.exports = {
  //...
  optimization: {
    nodeEnv: 'production'
  }
};

```

### optimization.mangleWasmImports

`bool: false`

当该配置项被设为true，webpack会通过引入更短的字符串来减少WASM文件的体积。它会对export和模块进行名字修饰。

PS.名字修饰的维基百科：[名字修饰(name mangling)的维基百科](https://zh.wikipedia.org/wiki/%E5%90%8D%E5%AD%97%E4%BF%AE%E9%A5%B0)

```
module.exports = {
  //...
  optimization: {
    mangleWasmImports: true
  }
};

```

### optimization.removeAvailableModules

`bool: true`

在该配置项影响下，Webpack会识别已经在父级chunk groups可用的子模块，将其从代码块中抽出。把该配置值设置为false以后，就会把该优化关闭。

```
module.exports = {
  //...
  optimization: {
    removeAvailableModules: false
  }
};

```


### optimization.removeEmptyChunks

`bool: true`

在该配置项影响下，Webpack会识别空的代码块，并将其移除。把该配置值设置为false以后，就会把该优化关闭。


```
module.exports = {
  //...
  optimization: {
    removeEmptyChunks: false
  }
};

```
### optimization.mergeDuplicateChunks

`bool: true`

在该配置项影响下，Webpack会把相等的代码块合并。把该配置值设置为false以后，就会把该优化关闭。

```
module.exports = {
  //...
  optimization: {
    mergeDuplicateChunks: false
  }
};

```

### optimization.flagIncludedChunks

`bool`

在该配置项影响下，Webpack会确定作为其他代码块子集的代码块，并且对这些代码块进行标记，即当加载较大的代码块时，不必加载子集。把该配置值设置为false以后，就会把该优化关闭。


```
module.exports = {
  //...
  optimization: {
    flagIncludedChunks: true
  }
};
```

### optimization.occurrenceOrder

`bool`

在该配置项影响下，Webpack会得出一个能最小化初始bundle体积的模块解析顺序。`optimization.occurrenceOrder`在生产环境下默认值为true，否则为false。


```
module.exports = {
  //...
  optimization: {
    occurrenceOrder: false
  }
};

```

### optimization.providedExports

`bool`

在该配置项的影响下，webpack会为`export * from ...`确定每一个`module`的`export`，用于其他代码优化或者代码生成。



```
module.exports = {
  //...
  optimization: {
    providedExports: false
  }
};

```

### optimization.usedExports

`bool`

确定每一个`module`的被使用过的 `export`。该配置项依赖于`optimization.providedExports`。这个信息被用于其他优化或者生成代码。`export`经过名字修饰的过程，它的名字会变成单个字符标识符。这对于`minmizer`消除无用代码很有帮助，并且能移除无用的`export`。在默认情况下，`optimization.concatenateModules `在生产环境下的值为true，其他情况下的值为false。



```
module.exports = {
  //...
  optimization: {
    usedExports: true
  }
};

```

### optimization.concatenateModules

`bool`

在该配置项影响下，webpack会尝试查找模块图中可以安全连接到单个模块中的段。该配置项依赖于`optimization.providedExports`和` optimization.usedExports`。在默认情况下，`optimization.concatenateModules `在生产环境下的值为true，其他情况下的值为false。


```
module.exports = {
  //...
  optimization: {
    flagIncludedChunks: true
  }
};
```

### optimization.sideEffects

`bool`
 
在该配置项影响下，webpack会读取`package.json`中的`sideEffects`标志位去跳过那些标记其不含副作用的模块。 

`package.json`

```
{
  "name": "awesome npm module",
  "version": "1.0.0",
  "sideEffects": false
}

```

这一配置项依赖`optimization.providedExports`和`optimization.usedExports`。这些依赖都在构建时间上有所开销，但是消除依赖能通过减少生成代码在性能上产生积极的影响。它也依赖于你自己的代码。为了更好的性能，去尝试吧。

在默认情况下，`optimization.sideEffects`在生产环境下的值为true，其他情况下的值为false。

`webpack.config.js`

```
module.exports = {
  //...
  optimization: {
    sideEffects: true
  }
};
```
