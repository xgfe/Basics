
	by luyang 2018.11
	
	
* AutomaticPrefetchPlugin
* ZopfliWebpackPlugin
* BannerPlugin
* ClosureWebpackPlugin
* CommonsChunkPlugin

# Webpack Plugins
## AutomaticPrefetchPlugin
> Q: A request for a normal module, which is resolved and built even before a require to it occurs. This can boost performance.
>
> A: @sokra: long chains of requires can form a chain where webpack will know about the last module very late. This could slow down the compilation because webpack has to wait for I/O (esp. resolving). The PrefetchPlugin starts modules earlier so I/O is less an concern.

与预先发现单个模块的`PrefetchPlugin`相比，`AutomaticPrefetchPlugin`在监听变化的同时，预先发现上一次编译中的所有模块，尝试改进增量构建时间。

由于增量构建时间非常快，不一定会提升性能。

### 使用
```js
// webpack.config.js
module.exports = {
  plugins: [
    new webpack.AutomaticPrefetchPlugin()
  ]
};
```

### 对比
```js
// PrefetchPlugin vs AutomaticPrefetchPlugin
let lastModules = null;
compiler.hooks.afterCompile.tap("AutomaticPrefetchPlugin", compilation => {
  lastModules = compilation.modules
    .filter(m => m instanceof NormalModule)
    .map(m => ({
      context: m.context,
      request: m.request
    }));
});

compiler.hooks.make.tapAsync(
  "AutomaticPrefetchPlugin", 
  (compilation, callback) => {
    if (!lastModules) return callback();
    asyncLib.forEach(
      lastModules,
      (m, callback) => {
        // PrefetchPlugin#compilation.prefetch
      },
      callback
    );
  }
);
```

## ZopfliWebpackPlugin
### 安装
`npm i -D zopfli-webpack-plugin`

### 使用
```js
var ZopfliPlugin = require("zopfli-webpack-plugin");
module.exports = {
    plugins: [
        new ZopfliPlugin({
            asset: "[path].gz[query]",
            algorithm: "zopfli",
            test: /\.(js|html)$/,
            threshold: 10240,
            minRatio: 0.8
        })
    ]
}
```

### 参数
- `asset`: [String] - 目标资产名称，默认值：`"[path].gz[query]"`
    + `[file]` - 原资产名
    + `[path]` - 原资产路径
    + `[query]`
- `filename`: [Function(asset)] - 接收经过参数处理的`asset`，返回新的资产名称，默认值：`false`
- `algorithm`: [String|Function(buf, option, callback)] - 算法
    + `String` - 算法来自zopfli
    + `Function(buf, option, callback)` - callback(err, result)
- `test`: [RegExp] - 处理成功匹配的asset，默认匹配所有asset
- `threshold`: [Number(bytes)] - 只处理大于阈值的资产，默认值：`0`
- `minRatio`: [Number] - 只处理可压缩比例低于阈值的资产，默认值：`0.8`
- `deleteOriginalAssets`: [Boolean] - 删除原资产，默认值：`false`

### 可选参数
- `verbose`: 详细日志，默认值：`false`,`
- `verbose_more`: 详细日志，默认值：`false`,`
- `numiterations`: 默认值：`15`
- `blocksplitting`: 默认值：`true`
- `blocksplittinglast`: 默认值：`false`
- `blocksplittingmax`: 默认值：`15`

## BannerPlugin
### 使用
```js
// webpack.config.js
module.exports = {
  plugins: [
    new webpack.BannerPlugin(banner || options)
  ]
};
```

### 参数
- `banner`: [String|Function(file)] - 注释内容
    + 占位符： hash, chunkhash, name, filebase, query, file,
- `raw`: [Boolean] - 取消内容注释，默认值：`false`
- `entryOnly`: [Boolean] - 仅写入entry chunks，默认值：`false`
- `test`: [String|RegExp|Array] - 匹配规则
- `include`: [String|RegExp|Array] - 包含文件
- `exclude`: [String|RegExp|Array] - 不包含文件


## ClosureWebpackPlugin
允许在Webpack中使用Google的Closure Tools
Closure-Compiler是一个完整的优化编译器和转换器。它提供无与伦比的优化，提供类型检查，并可轻松将转换为不同版本的ECMASCRIPT。

Closure-Library是一个实用程序库，旨在与Closure-Compiler完全兼容。

### 使用
```js
const ClosurePlugin = require('closure-webpack-plugin');

new ClosurePlugin({mode: 'STANDARD'}, {
  // compiler flags here
  //
  // for debuging help, try these:
  //
  // formatting: 'PRETTY_PRINT'
  // debug: true
})
```

### 参数
- `platform`: [native|java|javascript] - closure-compiler的使用版本，默认情况下，插件将尝试自动选择可用的最快选项
    + `JAVASCRIPT`: 不需要安装JVM。 并非所有标志都受支持
    + `JAVA`: 使用JVM及多个线程进行解析，从而加快大型构建的编译速度
    + `NATIVE`: 仅适用于Linux或MacOS。编译时间更快，无需JVM
- `mode`: [STANDARD(default)|AGGRESSIVE_BUNDLE|NONE] - 控制插件如何使用编译器
    + `STANDARD`: closure-compiler替代其他压缩器以及大多数Babel转换器
    + `AGGRESSIVE_BUNDLE`: 编译器执行模块的其他优化以生成更小的文件，但与所有输入模块不兼容
the compiler performs additional optimizations of modules to produce a much smaller file, but is not compatible with all input modules.
    + `NONE`: 为无编译器的开发模式设计，但仍然支持Closure-Library风格依赖
- `childCompilations`: [Boolean|Function] - 为了减少构建时间，默认情况下，此插件仅在主编译上运行。 诸如extract-text-plugin和html-webpack-plugin之类的插件作为子编译运行，通常不需要转换或压缩。默认值：`false`
    + `Function`: 对于特定控件，可以将该选项设置为将传递编译对象的函数。
```js
function(compilation) { 
  return /html-webpack/.test(compilation.name); 
}
```
- `closureLibraryBase`(optional): [String] - Closure-Library中base.js文件的路径
- `deps`(optional): [String|Array] - Closure-Library风格依赖关系映射，通常由Closure-Library附带的depswriter.py脚本生成
- `extraDeps`(optional): [Object] - 将名称空间映射到Closure-Library提供的名称空间的文件路径


### 编译器标记
该插件控制几个编译器标志。不应在任何模式下使用以下标志：

- module_resolution
- output_wrapper
- dependency_mode
- create_source_map
- module
- entry_point

### 激进捆绑模式
在此模式下，编译器会重写CommonJS模块和需要调用的提升程序。某些模块与此类重写不兼容。特别是，提升将导致以下代码无序执行：
```js
const foo = require('foo');
addPolyfillToFoo(foo);
const bar = require('bar');
```
Aggressive Bundle Mode利用自定义运行时，其中块文件中的模块都包含在同一范围内。 这避免了[小模块的成本](https://nolanlawson.com/2016/08/15/the-cost-of-small-modules/)

在Aggressive Bundle Mode中，文件只能出现在单个输出块中。使用Commons Chunk Plugin将重复的文件拆分为单个输出块。

### Tips for Use
- 不要使用babel - Closure-Compiler也是一个转换器。 如果您需要Closure-Compiler尚不支持的功能，请让babel仅针对这些功能。


## CommonsChunkPlugin
> The CommonsChunkPlugin 已经从 webpack v4 legato 中移除。想要了解在最新版本中如何处理 chunk，请查看 SplitChunksPlugin。

CommonsChunkPlugin 插件，是一个可选的用于建立一个独立文件(又称作 chunk)的功能，这个文件包括多个入口 chunk 的公共模块。

通过将公共模块拆出来，最终合成的文件能够在最开始的时候加载一次，便存到缓存中供后续使用。这个带来页面速度上的提升，因为浏览器会迅速将公共的代码从缓存中取出来，而不是每次访问一个新页面时，再去加载一个更大的文件。

`new webpack.optimize.CommonsChunkPlugin(options);`

### 参数
```js
{
  name: string, // or
  names: string[],
  // 这是 common chunk 的名称。已经存在的 chunk 可以通过传入一个已存在的 chunk 名称而被选择。
  // 如果一个字符串数组被传入，这相当于插件针对每个 chunk 名被多次调用
  // 如果该选项被忽略，同时 `options.async` 或者 `options.children` 被设置，所有的 chunk 都会被使用，
  // 否则 `options.filename` 会用于作为 chunk 名。
  // When using `options.async` to create common chunks from other async chunks you must specify an entry-point
  // chunk name here instead of omitting the `option.name`.

  filename: string,
  // common chunk 的文件名模板。可以包含与 `output.filename` 相同的占位符。
  // 如果被忽略，原本的文件名不会被修改(通常是 `output.filename` 或者 `output.chunkFilename`)。
  // This option is not permitted if you're using `options.async` as well, see below for more details.

  minChunks: number|Infinity|function(module, count) => boolean,
  // 在传入  公共chunk(commons chunk) 之前所需要包含的最少数量的 chunks 。
  // 数量必须大于等于2，或者少于等于 chunks的数量
  // 传入 `Infinity` 会马上生成 公共chunk，但里面没有模块。
  // 你可以传入一个 `function` ，以添加定制的逻辑（默认是 chunk 的数量）

  chunks: string[],
  // 通过 chunk name 去选择 chunks 的来源。chunk 必须是  公共chunk 的子模块。
  // 如果被忽略，所有的，所有的 入口chunk (entry chunk) 都会被选择。

  children: boolean,
  // 如果设置为 `true`，所有公共 chunk 的子模块都会被选择

  deepChildren: boolean,
  // 如果设置为 `true`，所有公共 chunk 的后代模块都会被选择

  async: boolean|string,
  // 如果设置为 `true`，一个异步的  公共chunk 会作为 `options.name` 的子模块，和 `options.chunks` 的兄弟模块被创建。
  // 它会与 `options.chunks` 并行被加载。
  // Instead of using `option.filename`, it is possible to change the name of the output file by providing
  // the desired string here instead of `true`.

  minSize: number,
  // 在 公共chunk 被创建立之前，所有 公共模块 (common module) 的最少大小。
}
```

### 例子
#### 公共chunk 用于 入口chunk (entry chunk)
生成一个额外的 chunk 包含入口chunk 的公共模块。
```js
new webpack.optimize.CommonsChunkPlugin({
  name: 'commons',
  // (公共 chunk(commnon chunk) 的名称)

  filename: 'commons.js',
  // (公共chunk 的文件名)

  // minChunks: 3,
  // (模块必须被3个 入口chunk 共享)

  // chunks: ["pageA", "pageB"],
  // (只使用这些 入口chunk)
});
```
你必须在 入口chunk 之前加载生成的这个 公共chunk:
```html
<script src="commons.js" charset="utf-8"></script>
<script src="entry.bundle.js" charset="utf-8"></script>
```

#### 明确第三方库 chunk
将你的代码拆分成公共代码和应用代码。
```js
module.exports = {
  //...
  entry: {
    vendor: ['jquery', 'other-lib'],
    app: './entry'
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      // filename: "vendor.js"
      // (给 chunk 一个不同的名字)

      minChunks: Infinity,
      // (随着 entry chunk 越来越多，
      // 这个配置保证没其它的模块会打包进 vendor chunk)
    })
  ]
};
```
```html
<script src="vendor.js" charset="utf-8"></script>
<script src="app.js" charset="utf-8"></script>
```
结合长期缓存，你可能需要使用这个插件去避免 公共chunk 改变。 你也需要使用 records 去保持稳定的模块 id，例如，使用 NamedModulesPlugin 或 HashedModuleIdsPlugin。

#### 将公共模块打包进父 chunk 
使用代码拆分功能，一个 chunk 的多个子 chunk 会有公共的依赖。为了防止重复，可以将这些公共模块移入父 chunk。这会减少总体的大小，但会对首次加载时间产生不良影响。如果预期到用户需要下载许多兄弟 chunks（例如，入口 trunk 的子 chunk），那这对改善加载时间将非常有用。
```js
new webpack.optimize.CommonsChunkPlugin({
  // names: ["app", "subPageA"]
  // (选择 chunks，或者忽略该项设置以选择全部 chunks)

  children: true,
  // (选择所有被选 chunks 的子 chunks)

  // minChunks: 3,
  // (在提取之前需要至少三个子 chunk 共享这个模块)
});
```

#### 额外的异步 公共chunk 
与上面的类似，但是并非将公共模块移动到父 chunk（增加初始加载时间），而是使用新的异步加载的额外公共chunk。当下载额外的 chunk 时，它将自动并行下载。
```js
new webpack.optimize.CommonsChunkPlugin({
  name: 'app',
  // or
  names: ['app', 'subPageA'],
  // the name or list of names must match the name or names
  // of the entry points that create the async chunks

  children: true,
  // (选择所有被选 chunks 的子 chunks)

  async: true,
  // (创建一个异步 公共chunk)

  minChunks: 3,
  // (在提取之前需要至少三个子 chunk 共享这个模块)
});
```

#### 给 minChunks 配置传入函数 
你也可以给 minChunks 传入一个函数。这个函数会被 CommonsChunkPlugin 插件回调，并且调用函数时会传入 module 和 count 参数。

module 参数代表每个 chunks 里的模块，这些 chunks 是你通过 name/names 参数传入的。 module类似NormalModule，有两个实用的属性；

- `module.context`: 文件目录，如：'/my_project/node_modules/example-dependency'
- `module.resource`: 被处理的文件名，如：'/my_project/node_modules/example-dependency/index.js'
count 参数表示 module 被使用的 chunk 数量。

当你想要对 CommonsChunk 如何决定模块被打包到哪里的算法有更为细致的控制， 这个配置就会非常有用。
```js
new webpack.optimize.CommonsChunkPlugin({
  name: 'my-single-lib-chunk',
  filename: 'my-single-lib-chunk.js',
  minChunks: function(module, count) {
    // 如果模块是一个路径，而且在路径中有 "somelib" 这个名字出现，
    // 而且它还被三个不同的 chunks/入口chunk 所使用，那请将它拆分到
    // 另一个分开的 chunk 中，chunk 的 keyname 是 "my-single-lib-chunk"，而文件名是 "my-single-lib-chunk.js"
    return module.resource && (/somelib/).test(module.resource) && count === 3;
  }
});
```

正如上面看到的，这个例子允许你只将其中一个库移到一个分开的文件当中，当而仅当函数中的所有条件都被满足了。

This concept may be used to obtain implicit common vendor chunks:
```js
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  minChunks: function (module) {
    // this assumes your vendor imports exist in the node_modules directory
    return module.context && module.context.includes('node_modules');
  }
});
```
In order to obtain a single CSS file containing your application and vendor CSS, use the following minChunks function together with ExtractTextPlugin:
```js
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  minChunks: function (module) {
    // This prevents stylesheet resources with the .css or .scss extension
    // from being moved from their original chunk to the vendor chunk
    if(module.resource && (/^.*\.(css|scss)$/).test(module.resource)) {
      return false;
    }
    return module.context && module.context.includes('node_modules');
  }
});
```

### Manifest file 
将webpack引导文件提取到单独文件中，可以使用名字（name）未在入口（entry）定义的CommonsChunkPlugin。通常使用manifest命名，详述参见缓存指南。
```js
new webpack.optimize.CommonsChunkPlugin({
  name: 'manifest',
  minChunks: Infinity
});
```

### 结合使用公共文件和缓存文件
因为vendor和manifest使用了不同的minChunks定义，需要引入两次插件。
```js
[
  new webpack.optimize.CommonsChunkPlugin({
    name: 'vendor',
    minChunks: function(module){
      return module.context && module.context.includes('node_modules');
    }
  }),
  new webpack.optimize.CommonsChunkPlugin({
    name: 'manifest',
    minChunks: Infinity
  }),
];
```

### More Examples
- [Common and Vendor Chunks](https://github.com/webpack/webpack/tree/master/examples/common-chunk-and-vendor-chunk)
- [Multiple Common Chunks](https://github.com/webpack/webpack/tree/8b888fedfaeaac6bd39168c0952cc19e6c34280a/examples/multiple-commons-chunks)
- [Multiple Entry Points with Commons Chunk](https://github.com/webpack/webpack/tree/8b888fedfaeaac6bd39168c0952cc19e6c34280a/examples/multiple-entry-points-commons-chunk-css-bundle)