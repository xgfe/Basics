	by zhouyinian 2018.12

* BabelMinifyWebpackPlugin
* IgnorePlugin
* LimitChunkCountPlugin
* LoaderOptionsPlugin
* MinChunkSizePlugin
* ModuleConcatenationPlugin

## BabelMinifyWebpackPlugin
一个用于babel-minify的 webpack 插件 - 基于 babel 的 minifier

###安装
`npm install babel-minify-webpack-plugin --save-dev`

### 用法
```
// webpack.config.js
const MinifyPlugin = require("babel-minify-webpack-plugin");
module.exports = {
  entry: //...,
  output: //...,
  plugins: [
    new MinifyPlugin(minifyOpts, pluginOpts)
  ]
}
```

### 选项
minifyOpts 被传递给 babel-preset-minify。 你可以在包目录中找到所有可用的选项。

Default: {} 

### pluginOpts

- `test`:  JS文件扩展名正则表达式。 默认: /\.js($|\?)/i
- `include`: 包含的文件，默认 undefined
-  `exclude`:  排除的文件，默认 undefined
- `comments`:  保留注释。 默认: /^\**!|@preserve|@license|@cc_on/, falsy 值将移除所有注释。可以接受函数，带有测试属性的（正则）的对象和值。
- `sourceMap`:  默认: 使用 webpackConfig.devtool。 这里的设置会覆写devtool的设置。
- `parserOpts`:  配置具有特殊解析器选项的babel。
- `babel`:  传入一个自定义的 babel-core，代替原来的。 require("babel-core")
- `minifyPreset` :  传入一个自定义的 minify preset，代替原来的。 - require("babel-preset-minify").



你也可以在webpack中使用babel-loader，引入 minify 作为一个预设并且应该运行的更快 - 因为 babel-minify 将运行在更小的文件。但是，这个插件为什么还存在呢？

- webpack loader 对单个文件进行操作，并且 minify preset 作为一个 webpack loader将会把每个文件视为在浏览器全局范围内直接执行（默认情况下），并且不会优化顶级作用域内的某些内容。要在文件的顶级作用域内进行优化，请在 minifyOptions 中设置 mangle: { topLevel: true }。
- 当你排除 node_modules 不通过 babel-loader 运行时，babel-minify 优化不会应用于被排除的文件，因为它们不会通过 minifier。
- 当你使用带有 webpack 的 babel-loader 时，由 webpack 为模块系统生成的代码不会通过 loader，并且不会通过 babel-minify 进行优化。
- 一个 webpack 插件可以在整个 chunk/bundle 输出上运行，并且可以优化整个bundle，你可以看到一些细微的输出差异。但是，由于文件大小通常非常大，所以会慢很多。所以这里有一个想法 - 我们可以将一些优化作为 loader 的一部分，并在插件中进行一些优化。


## LimitChunkCountPlugin
当你在编写代码时，可能已经添加了许多代码分离点(code split point)来实现按需加载(load stuff on demand)。在编译完之后，你可能会注意到有一些很小的 chunk - 这产生了大量 HTTP 请求开销。幸运的是，此插件可以通过合并的方式，后处理你的 chunk，以减少请求数。

```
new webpack.optimize.LimitChunkCountPlugin({
  // 选项……
})
```

### 选项
选项可以支持以下：


### maxChunks
使用大于或等于 1 的值，来限制 chunk 的最大数量。使用 1 防止添加任何其他额外的 chunk，这是因为 entry/main chunk 也会包含在计数之中。

```
const webpack = require('webpack');
module.exports = {
  // ...
  plugins: [
    new webpack.optimize.LimitChunkCountPlugin({
      maxChunks: 5
    })
  ]
};

```

### minChunkSize
设置 chunk 的最小大小。

```
new webpack.optimize.LimitChunkCountPlugin({
  maxChunks: 5, // 必须大于或等于 1
  minChunkSize: 1000
})
```

### 命令行接口(CLI)
此插件和其选项还可以通过命令行接口(CLI)执行：

`--optimize-max-chunks 15` 
或
`--optimize-min-chunk-size 10000`


## LoaderOptionsPlugin

loader-options-plugin 和其他插件不同，它用于将 webpack 1 迁移至 webpack 2。在 webpack 2 中，对 webpack.config.js 的结构要求变得更加严格；不再开放扩展给其他的 loader/插件。webpack 2 推荐的使用方式是直接传递 options 给 loader/插件（换句话说，配置选项将不是全局/共享的）。

不过，在某个 loader 升级为依靠直接传递给它的配置选项运行之前，可以使用 loader-options-plugin 来抹平差异。你可以通过这个插件配置全局/共享的 loader 配置，使所有的 loader 都能收到这些配置。

```
new webpack.LoaderOptionsPlugin({
  // Options...
})
```

**将来这个插件可能会被移除，因为它只是用于迁移**
### 选项
此插件支持以下选项：

- `options.debug (boolean)`：loader 是否为 debug 模式。debug 在 webpack 3 中将被移除。
- `options.minimize (boolean)`：loader 是否要切换到优化模式。
- `options.options (object)`：一个配置对象，用来配置旧的 loader - 将使用和 webpack.config.js 相同的结构。
- `options.options.context (string)`：配置 loader 时使用的上下文。
- 任何其他选项和在 webpack.config.js 中一样……

### 用法
关于此插件可能的用法，这里有个示例：

```
new webpack.LoaderOptionsPlugin({
  minimize: true,
  debug: false,
  options: {
    context: __dirname
  }
})

```

## MinChunkSizePlugin
通过合并小于 minChunkSize 大小的 chunk，将 chunk 体积保持在指定大小限制以上

```
new webpack.optimize.MinChunkSizePlugin({
  minChunkSize: 10000 // Minimum number of characters
})
```

## MiniCssExtractPlugin
该插件用于将css提取到单独的文件。为每个包含css的JS文件创建一个css文件。支持css按需加载。

需要webpack 版本 4 以上
对比extract-text-webpack-plugin:

- 异步加载
- 没有重复的编译（性能）
- 更容易使用
- 特定于css

### 安装
`npm install --save-dev mini-css-extract-plugin`

### 简单例子
```
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              // you can specify a publicPath here
              // by default it use publicPath in webpackOptions.output
              publicPath: '../'
            }
          },
          "css-loader"
        ]
      }
    ]
  }
}
```

### 高级配置示例
该插件应当仅用于production模式下，并且不需要使用style-loader。特别是当你想要在development下使用HMR。

下面是一个示例，既可以在开发中使用HMR，也可以在生成版本的文件中提取样式。

```
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const devMode = process.env.NODE_ENV !== 'production'

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: devMode ? '[name].css' : '[name].[hash].css',
      chunkFilename: devMode ? '[id].css' : '[id].[hash].css',
    })
  ],
  module: {
    rules: [
      {
        test: /\.(sa|sc|c)ss$/,
        use: [
          devMode ? 'style-loader' : MiniCssExtractPlugin.loader,
          'css-loader',
          'postcss-loader',
          'sass-loader',
        ],
      }
    ]
  }
}
```

### 压缩
在webpack5很可能会内置css压缩，目前在webpack4还需要手动配置。为了压缩输出，可使用optimize-css-assets-webpack-plugin插件。

默认只压缩js，所以通过设置optimization.minimizer覆盖默认的压缩配置，同时应确保指定了一个JS的压缩配置。

```
const UglifyJsPlugin = require("uglifyjs-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
module.exports = {
  optimization: {
    minimizer: [
      new UglifyJsPlugin({
        cache: true,
        parallel: true,
        sourceMap: true // set to true if you want JS source maps
      }),
      new OptimizeCSSAssetsPlugin({})
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          "css-loader"
        ]
      }
    ]
  }
}

```

### 特点

### 在单个文件中提取所有css
类似extract-text-webpack-plugin，通过optimization.splitChunks.cacheGroups可以将css提取到一个文件中

```
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  optimization: {
    splitChunks: {
      cacheGroups: {
        styles: {
          name: 'styles',
          test: /\.css$/,
          chunks: 'all',
          enforce: true
        }
      }
    }
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css",
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          "css-loader"
        ]
      }
    ]
  }
}
```

### 基于入口提取css
可以根据entry name提取css。例如当动态根据路由import相关代码的时。并且也防止了ExtractTextPlugin中的css重复的问题。

```
const path = require('path');
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

function recursiveIssuer(m) {
  if (m.issuer) {
    return recursiveIssuer(m.issuer);
  } else if (m.name) {
    return m.name;
  } else {
    return false;
  }
}

module.exports = {
  entry: {
    foo: path.resolve(__dirname, 'src/foo'),
    bar: path.resolve(__dirname, 'src/bar')
  },
  optimization: {
    splitChunks: {
      cacheGroups: {
        fooStyles: {
          name: 'foo',
          test: (m,c,entry = 'foo') => m.constructor.name === 'CssModule' && recursiveIssuer(m) === entry,
          chunks: 'all',
          enforce: true
        },
        barStyles: {
          name: 'bar',
          test: (m,c,entry = 'bar') => m.constructor.name === 'CssModule' && recursiveIssuer(m) === entry,
          chunks: 'all',
          enforce: true
        }
      }
    }
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css",
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          "css-loader"
        ]
      }
    ]
  }
}

```

## ModuleConcatenationPlugin
过去 webpack 打包时的一个取舍是将 bundle 中各个模块单独打包成闭包。这些打包函数使你的 JavaScript 在浏览器中处理的更慢。相比之下，一些工具像 Closure Compiler 和 RollupJS 可以提升(hoist)或者预编译所有模块到一个闭包中，提升你的代码在浏览器中的执行速度。

这个插件会在 webpack 中实现以上的预编译功能。

`new webpack.optimize.ModuleConcatenationPlugin()`

>这种连结行为被称为“作用域提升(scope hoisting)”。
由于实现 ECMAScript 模块语法，作用域提升(scope hoisting)这个特定于此语法的功能才成为可能。webpack 可能会根据你正在使用的模块类型和其他的情况，回退到普通打包

**记住，此插件仅适用于由 webpack 直接处理的 ES6 模块。在使用转译器(transpiler)时，你需要禁用对模块的处理（例如 Babel 中的 modules 选项）。**

### 绑定失败的优化[Optimization Bailouts]
像文章中解释的，webpack 试图达到分批的作用域提升(scope hoisting)。它会将一些模块绑定到一个作用域内，但并不是任何情况下都会这么做。如果 webpack 不能绑定模块，将会有两个选择 Prevent 和 Root，Prevent 意思是模块必须在自己的作用域内。 Root 意味着将创建一个新的模块组。以下条件决定了输出结果：

Condition | Outcome
----------|--------
Non ES6 Module | Prevent
Imported By Non Import | Root
Imported From Other Chunk | Root
Imported By Multiple Other Module Groups | Root
Imported With import() | Root
Affected By ProvidePlugin Or Using module | Prevent
HMR Accepted | Root
Using eval() | Prevent
In Multiple Chunks | Prevent
export * from "cjs-module"|Prevent

### 模块分组算法[Module Grouping Algorithm]
以下 JavaScript 伪代码解释了算法：

```
modules.forEach(module => {
  const group = new ModuleGroup({
    root: module
  });
  module.dependencies.forEach(dependency => {
    tryToAdd(group, dependency);
  });
  if (group.modules.length > 1) {
    orderedModules = topologicalSort(group.modules);
    concatenatedModule = new ConcatenatedModule(orderedModules);
    chunk.add(concatenatedModule);
    orderedModules.forEach(groupModule => {
      chunk.remove(groupModule);
    });
  }
});

function tryToAdd(group, module) {
  if (group.has(module)) {
    return true;
  }
  if (!hasPreconditions(module)) {
    return false;
  }
  const nextGroup = group;
  const result = module.dependents.reduce((check, dependent) => {
    return check && tryToAdd(nextGroup, dependent);
  }, true);
  if (!result) {
    return false;
  }
  module.dependencies.forEach(dependency => {
    tryToAdd(group, dependency);
  });
  group.merge(nextGroup);
  return true;
}
```
### 优化绑定失败的调试[Debugging Optimization Bailouts]
当我们使用 webpack CLI 时，加上参数 --display-optimization-bailout 将显示绑定失败的原因。在 webpack 配置里，只需将以下内容添加到 stats 对象中：

```
{
  ...stats,
  // Examine all modules
  maxModules: Infinity,
  // Display bailout reasons
  optimizationBailout: true
}
```


