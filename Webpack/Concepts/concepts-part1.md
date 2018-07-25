	by yuxue 2018.07
	
### 目录：

* webpack概念
* 入口
	* 默认值、 对象、 字符串、数组、常见场景(实例演示)
* 输出
	* 基本用法、 高级进阶（实例演示）
* 模式
	* 模式概念、配置方式（实例演示）
* loader
	* loader概念、 3种配置方式、loader特点、解析loader（实例演示）
* plugin
	* 概念、剖析、用法、（实例演示）

## webpack

本质上，webpack是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。
当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

## webpack有哪些概念
在配置使用webpack之前我们需要先理解四个核心概念

- 入口(entry)
- 输出(output)
- loader
- 插件(plugins)

接下来会给出这些概念的高度概述，同时提供具体概念的详尽相关用例

##入口

入口起点(entry point)指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

每个依赖项随即被处理，最后输出到称之为 bundles 的文件中，我们将在下一章节详细讨论这个过程。

可以通过在 webpack 配置中配置 entry 属性，来指定一个入口起点（或多个入口起点）。默认值为 ./src。

接下来我们看一个 entry 配置的最简单例子：

![](http://vfile.meituan.net/xgfe/9e10a0591f5d34f84a03bf526e2cdd2726278.png)

***根据应用程序的特定需求，可以以多种方式配置 entry 属性。从入口起点章节可以了解更多信息。***


##出口

output 属性告诉 webpack 在哪里输出它所创建的 bundles，以及如何命名这些文件，默认值为 ./dist。

基本上，整个应用程序结构，都会被编译到你指定的输出路径的文件夹中。

你可以通过在配置中指定一个 output 字段，来配置这些处理过程：

![](http://vfile.meituan.net/xgfe/067a7bd4c0be071f7c2354e399b03f2e72673.png)

在上面的示例中，我们通过 output.filename 和 output.path 属性，来告诉 webpack bundle 的名称，以及我们想要 bundle 生成(emit)到哪里。

可能你想要了解在代码最上面导入的 path 模块是什么，它是一个 Node.js 核心模块，用于操作文件路径。


***你可能会发现术语生成(emitted 或 emit)贯穿了我们整个文档和插件 API。它是“生产(produced)”或“释放(discharged)”的特殊术语。***

***output 属性还有更多可配置的特性，如果你想要了解更多关于 output 属性的概念，你可以通过阅读概念章节来了解更多。***


##loader

loader 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。loader 可以将所有类型的文件转换为 webpack 能够处理的有效模块，然后你就可以利用 webpack 的打包能力，对它们进行处理。

本质上，webpack loader 将所有类型的文件，转换为应用程序的依赖图（和最终的 bundle）可以直接引用的模块。

***注意，loader 能够 import 导入任何类型的模块（例如 .css 文件），这是 webpack 特有的功能，其他打包程序或任务执行器的可能并不支持。我们认为这种语言扩展是有很必要的，因为这可以使开发人员创建出更准确的依赖关系图。***

在更高层面，在 webpack 的配置中 loader 有两个目标：

1.test 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。

2.use 属性，表示进行转换时，应该使用哪个 loader。

![](http://vfile.meituan.net/xgfe/4f74f023bcf02829eb5cb5b87bb48e3b80384.png)

以上配置中，对一个单独的 module 对象定义了 rules 属性，里面包含两个必须属性：test 和 use。这告诉 webpack 编译器(compiler) 如下信息：

“嘿，webpack 编译器，当你碰到「在 require()/import 语句中被解析为 '.txt' 的路径」时，在你对它打包之前，先使用 raw-loader 转换一下。”

***重要的是要记得，在 webpack 配置中定义 loader 时，要定义在 module.rules 中，而不是 rules。然而，在定义错误时 webpack 会给出严重的警告。为了使你受益于此，如果没有按照正确方式去做，webpack 会“给出严重的警告”***

##模式
通过选择 development 或 production 之中的一个，来设置 mode 参数，你可以启用相应模式下的 webpack 内置的优化

![](http://vfile.meituan.net/xgfe/04c532bbfc51cad1f3d8b489a341ac1f20339.png)

##插件

loader 被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务。

想要使用一个插件，你只需要 require() 它，然后把它添加到 plugins 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 new 操作符来创建它的一个实例。

![](http://vfile.meituan.net/xgfe/5008c5fbebaf0a949129dc32b84049f6137075.png)

***webpack 提供许多开箱可用的插件！查阅我们的插件列表获取更多信息。***

***在 webpack 配置中使用插件是简单直接的，然而也有很多值得我们进一步探讨的用例。***


## 入口 （依赖图的开始）
正如我们在起步中提到的，在 webpack 配置中有多种方式定义 entry 属性。除了解释为什么它可能非常有用，我们还将向你展示如何去配置 entry 属性。

entry接受三种形式的值：对象，字符串，数组

### 默认值的形式

在webpack中不配置entry选项

```
module.exports = {
	output: {
		filename: 'entry.js',
		
		path: path.resolve(__dirname, 'dist')
	},
	module: {
		rules: [{
			test: /\.css$/,
			use: [
				'style-loader',
				'css-loader'
			]
		}]
	},
	plugins: [
		new CleanWebpackPlugin(['dist']),
		new HtmlWebpackPlugin({
			title: 'Output Management'
		})
	]
};
```

以上配置打包后的目录是这样的（dist是打包后的目录，src是打包前资源目录）

![](http://vfile.meituan.net/xgfe/1848f645b4059befbd0a4522f023540c43552.png)


默认去找src目录下的index.js去打包

### entry入口的对象形式

对象语法会比较繁琐。但是这也是应用程序中定义入口的**最可扩展**的方式。

“可扩展的 webpack 配置”是指，可重用并且可以与其他配置组合使用。这是一种流行的技术，用于将关注点(concern)从环境(environment)、构建目标(build target)、运行时(runtime)中分离。然后使用专门的工具（如 webpack-merge）将它们合并。

**key**

对象形式中对于key可以是简单的字符串，比如：'app', 'vebdors', 'entry-1'等。

```
entry: {
	'entryTest': './src/entry-1.js'
},
output: {
	filename: '[name].js',
	path: path.resolve(__dirname, 'dist')
},
```
上面的配置打包后生成

![](http://vfile.meituan.net/xgfe/7807164cfe41880711b4791811e62e8c14810.png)


**value**

value可以是字符串，这个字符串可以是文件路径，也可以是一个npm模块

```
entry: {
	'my-lodash': 'lodash'
},
output: {
	filename: '[name].js',
	path: path.resolve(__dirname, 'dist')
},
```

上面的配置打包之后会生成

![](http://vfile.meituan.net/xgfe/ad2e39fd34c7148679584263eeda0e9815979.png)


value也可以是数组，数组中元素需要是合理字符串值。

```
entry: {
    main: ['./src/index.js', 'lodash']
},
output: {
	filename: '[name].js',
	path: path.resolve(__dirname, 'dist')
}
```

上面的配置打包生成

![](http://vfile.meituan.net/xgfe/ac515713ac9e06d76e055ef49e9d1fa513703.png)
### 字符串形式entry

```
const config = {
  entry: './path/to/my/entry/file.js'
};

module.exports = config;

```
字符串形式等于是下面这种形式的简写

```
const config = {
  entry: {
    main: './path/to/my/entry/file.js'
  }
};
```

### 数组形式entry

```
const config = {
	entry: ['./app.js', 'lodash']
};
```

等价于下面的对象形式

```
const config = {
	entry: {
		main: ['./app.js', 'lodash']
	}
};
```

```
entry: ['./src/print.js', './src/index.js'],
output: {
	filename: '[name].js',
	path: path.resolve(__dirname, 'dist')
},
```

上面的配置打包后生成

![](http://vfile.meituan.net/xgfe/65e51a0e0bb62df18f157bf38e2fe4e814057.png)


"当你向 entry 传入一个数组时会发生什么？向 entry 属性传入「文件路径(file path)数组」将创建“多个主入口(multi-main entry)”。在你想要多个依赖文件一起注入，并且将它们的依赖导向(graph)到一个“chunk”时，传入数组的方式就很有用。"

### 常见场景

**1.分离 应用程序(app) 和 第三方库(vendor) 入口**

```
const config = {
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js'
  }
};
```
这是什么？从表面上看，这告诉我们 webpack 从 app.js 和 vendors.js 开始创建依赖图(dependency graph)。这些依赖图是彼此完全分离、互相独立的（每个 bundle 中都有一个 webpack 引导(bootstrap)）。这种方式比较常见于，只有一个入口起点（不包括 vendor）的单页应用程序(single page application)中。

为什么？此设置允许你使用 CommonsChunkPlugin 从「应用程序 bundle」中提取 vendor 引用(vendor reference) 到 vendor bundle，并把引用 vendor 的部分替换为 ___webpack_require___() 调用。如果应用程序 bundle 中没有 vendor 代码，那么你可以在 webpack 中实现被称为长效缓存的通用模式。

**2.多页面应用程序**

``` 
const config = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};

```
这是什么？我们告诉 webpack 需要 3 个独立分离的依赖图（如上面的示例）。

为什么？在多页应用中，服务器将为你获取一个新的 HTML 文档。页面重新加载新文档，并且资源被重新下载。然而，这给了我们特殊的机会去做很多事：

- 使用 CommonsChunkPlugin 为每个页面间的应用程序共享代码创建 bundle。由于入口起点增多，多页应用能够复用入口起点之间的大量代码/模块，从而可以极大地从这些技术中受益。

***根据经验：每个 HTML 文档只使用一个入口起点。***

## 输出（output）

配置 output 选项可以控制 webpack 如何向硬盘写入编译文件。注意，即使可以存在多个入口起点，但只指定一个输出配置

在 webpack 中配置 output 属性的最低要求是，将它的值设置为一个对象，包括以下两点：

- filename 用于输出文件的文件名。
- 目标输出目录 path 的绝对路径。

**用法**

```
const config = {
  output: {
    filename: 'bundle.js',
    path: '/home/proj/public/assets'
  }
};

module.exports = config;
```
此配置将一个单独的 bundle.js 文件输出到 /home/proj/public/assets 目录中。

**多个入口起点**

如果配置创建了多个单独的 "chunk"（例如，使用多个入口起点或使用像 CommonsChunkPlugin 这样的插件），则应该使用占位符(substitutions)来确保每个文件具有唯一的名称

```
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
}
// 写入到硬盘：./dist/app.js, ./dist/search.js
```
**高级进阶**

以下是使用 CDN 和资源 hash 的复杂示例：

```
output: {
  path: "/home/proj/cdn/assets/[hash]",
  publicPath: "http://cdn.example.com/assets/[hash]/"
}
```

```
entry: {
		index: './src/index.js',
		print: './src/print.js'
},
output: {
	filename: '[name].js',
	path: path.resolve(__dirname, 'dist/[hash]'),
	publicPath: "http://cdn.example.com/assets/[hash]"
}
```
以上配置输出目录为

![](http://vfile.meituan.net/xgfe/f7dc0c16df75ef92ea264f82deac1b3931211.png)

在html中引用的文件路径变为

![](http://vfile.meituan.net/xgfe/e12d3a9c991e084691950fda40cc33ea126604.png)


publicPath指定的是构建后在html里的路径，一般也是用这个来指定上线后的cdn域名
在编译时不知道最终输出文件的 publicPath 的情况下，publicPath 可以留空，并且在入口起点文件运行时动态设置。可以在入口起点设置 
```
__webpack_public_path__ = myRuntimePublicPath
```

## 模式[mode]
提供 mode 配置选项，告知 webpack 使用相应模式的内置优化。他的值是string类型。分别为'none', 'development', 'production'(默认).

**development**

会将 process.env.NODE_ENV 的值设为 development。启用 NamedChunksPlugin 和 NamedModulesPlugin。如下图所示

![](http://vfile.meituan.net/xgfe/11b6b771c0dac77dded2b4cddd95f55091160.png)

**production**

会将 process.env.NODE_ENV 的值设为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin.

![](http://vfile.meituan.net/xgfe/f48a7426d1523e7a43328a48efdf3ec5106324.png)

**none**

Opts out of any default optimization options

如果未设置，webpack将production设置为mode的默认值

![](http://vfile.meituan.net/xgfe/0d1fd622f372f32cc6009cb4c2c90e2e36465.png)

###两种配置形式

1.在配置文件中

```
module.exports = {
  mode: 'production'
};
```
2.从 CLI 参数中传递

```
webpack --mode=production
```

如果要根据webpack.config.js中的mode变量更改行为，则必须导出函数而不是对象

![](http://vfile.meituan.net/xgfe/e935c162e79f6482a1e7a833ced0158988780.png)


***记住，只设置 NODE_ENV，则不会自动设置 mode。***


命令行打包信息如下

![](http://vfile.meituan.net/xgfe/7486d5c9cbefafe743b4685959446373160684.png)



## loader


loader 用于对模块的源代码进行转换。loader 可以使你在 import 或"加载"模块时预处理文件。因此，loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。

- loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript。
- 将内联图像转换为 data URL。
- 允许你直接在 JavaScript 模块中 import CSS文件

你可以使用 loader 告诉 webpack 加载 CSS 文件，或者将 TypeScript 转为 JavaScript。为此，首先安装相对应的 loader

```
npm install --save-dev css-loader
npm install --save-dev ts-loader
```
然后指示 webpack 对每个 .css 使用 css-loader，以及对所有 .ts 文件使用 ts-loader

```
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' }
    ]
  }
};
```
**使用loader**

- 配置（推荐）：在 webpack.config.js 文件中指定 loader。
- 内联：在每个 import 语句中显式指定 loader。
- CLI：在 shell 命令中指定它们。

1、配置（推荐）：在 webpack.config.js 文件中指定 loader。

module.rules 允许你在 webpack 配置中指定多个 loader。 这是展示 loader 的一种简明方式，并且有助于使代码变得简洁。同时让你对各个 loader 有个全局概览：

```
module: {
rules: [
  {
    test: /\.css$/,
    use: [
      { loader: 'style-loader' },
      {
        loader: 'css-loader',
        options: {
          modules: true
        }
      }
    ]
  }
]
}
```
经过以上配置我们在js里引入的css就如下图所示添加到html文件中。

![](http://vfile.meituan.net/xgfe/520267e4fc4cc2a007352f84d76e415d39972.png)


2、内联：在每个 import 语句中显式指定 loader。

```
import Styles from 'style-loader!css-loader?modules!./styles.css';
```

可以在 import 语句或任何等效于 "import" 的方式中指定 loader。使用 ! 将资源中的 loader 分开。分开的每个部分都相对于当前目录解析。

选项可以传递查询参数，例如 ?key=value&foo=bar，或者一个 JSON 对象，例如 ?{"key":"value","foo":"bar"}。

eg:在config.js去除关于css-loader和style-loader的配置,然后在引入css的文件中配置如下：

```
import _ from 'lodash';
// import './style.css';
import 'style-loader!css-loader?modules!./loader-inline.css';
```
生成的效果

![](http://vfile.meituan.net/xgfe/520267e4fc4cc2a007352f84d76e415d39972.png)


尽可能使用 module.rules，因为这样可以减少源码中的代码量，并且可以在出错时，更快地调试和定位 loader 中的问题。

3、CLI：在 shell 命令中指定它们。

```
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
```
这会对 .jade 文件使用 jade-loader，对 .css 文件使用 style-loader 和 css-loader。

**loader 特性**

- loader 支持链式传递。能够对资源使用流水线(pipeline)。一组链式的 loader 将按照相反的顺序执行。loader 链中的第一个 loader 返回值给下一个 loader。在最后一个 loader，返回 webpack 所预期的 JavaScript。
- loader 可以是同步的，也可以是异步的。
- loader 运行在 Node.js 中，并且能够执行任何可能的操作。
- loader 接收查询参数。用于对 loader 传递配置。
- loader 也能够使用 options 对象进行配置。
- 除了使用 package.json 常见的 main 属性，还可以将普通的 npm 模块导出为 loader，做法是在 package.json 里定义一个 loader 字段。
- 插件(plugin)可以为 loader 带来更多特性。
- loader 能够产生额外的任意文件。

loader 通过（loader）预处理函数，为 JavaScript 生态系统提供了更多能力。 用户现在可以更加灵活地引入细粒度逻辑，例如压缩、打包、语言翻译和其他更多。

**解析 loader**

loader 遵循标准的模块解析。多数情况下，loader 将从模块路径（通常将模块路径认为是 npm install, node_modules）解析。

loader 模块需要导出为一个函数，并且使用 Node.js 兼容的 JavaScript 编写。通常使用 npm 进行管理，但是也可以将自定义 loader 作为应用程序中的文件。按照约定，loader 通常被命名为 xxx-loader（例如 json-loader）。有关详细信息，请查看如何编写 loader？。

## plugin

插件是 webpack 的支柱功能。webpack 自身也是构建于，你在 webpack 配置中用到的相同的插件系统之上！

插件目的在于解决 loader 无法实现的其他事。

**剖析**

webpack 插件是一个具有 apply 属性的 JavaScript 对象。apply 属性会被 webpack compiler 调用，并且 compiler 对象可在整个编译生命周期访问。

ConsoleLogOnBuildWebpackPlugin.js


```
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';

class ConsoleLogOnBuildWebpackPlugin {
    apply(compiler) {
        compiler.hooks.run.tap(pluginName, compilation => {
            console.log("webpack 构建过程开始！");
        });
    }
}
```

compiler hook 的 tap 方法的第一个参数，应该是驼峰式命名的插件名称。建议为此使用一个常量，以便它可以在所有 hook 中复用。

**用法**

由于插件可以携带参数/选项，你必须在 webpack 配置中，向 plugins 属性传入 new 实例。

webpack.config.js

```
const HtmlWebpackPlugin = require('html-webpack-plugin'); //通过 npm 安装
const webpack = require('webpack'); //访问内置的插件
const path = require('path');

const config = {
  entry: './path/to/my/entry/file.js',
  output: {
    filename: 'my-first-webpack.bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        use: 'babel-loader'
      }
    ]
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```

**Node API**

***即便使用 Node API，用户也应该在配置中传入 plugins 属性。compiler.apply 并不是推荐的使用方式。***

some-node-script.js

```
  const webpack = require('webpack'); //访问 webpack 运行时(runtime)
  const configuration = require('./webpack.config.js');

  let compiler = webpack(configuration);
  compiler.apply(new webpack.ProgressPlugin());

  compiler.run(function(err, stats) {
    // ...
  });
```

***你知道吗：以上看到的示例和 webpack 自身运行时(runtime) 极其类似。wepback 源码中隐藏有大量使用示例，你可以用在自己的配置和脚本中。***
