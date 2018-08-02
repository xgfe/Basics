	by matengfei 2018.07
	
## 目录：
* 配置
	* 使用不同的配置文件
	* 选项
* 使用不同的语言进行配置
	* TypeScript
	* CoffeeScript
	* Babel and JSX
* 多种配置类型
	* 导出为一个函数
	* 导出一个Promise
	* 导出多个配置对象
* 入口和上下文
	* context
	* entry
	* 命名

## 配置
Webpack不会要求您使用配置文件。但是，它将假定您的项目的入口点是src/index.js，并将结果输出到dist/main.js。mode为production模式，会进行代码压缩和优化。

通常，您的项目需要扩展此功能，为此您可以在根文件夹中创建Webpack.config.js文件，Webpack将自动使用它。
### 使用不同的配置文件

如果出于某种原因，您想根据特定情况使用不同的配置文件，可以使用--config标记通过命令行更改此配置文件。

package.json

```
"scripts": {
  "build": "webpack --config prod.config.js"
}
```

### 选项
webpack 是需要传入一个配置对象(configuration object)。取决于你如何使用 webpack，可以通过两种方式之一：终端或 Node.js。下面指定了所有可用的配置选项。

> 注意整个配置中我们使用 Node 内置的 path 模块，并在它前面加上 __dirname这个全局变量。可以防止不同操作系统之间的文件路径问题，并且可以使相对路径按照预期工作。


```
const path = require('path');

module.exports = {
  mode: "production", // "production" | "development" | "none"  // Chosen mode tells webpack to use its built-in optimizations accordingly.

  entry: "./app/entry", // string | object | array  // 这里应用程序开始执行
  // webpack 开始打包

  output: {
    // webpack 如何输出结果的相关选项

    path: path.resolve(__dirname, "dist"), // string
    // 所有输出文件的目标路径
    // 必须是绝对路径（使用 Node.js 的 path 模块）

    filename: "bundle.js", // string    // 「入口分块(entry chunk)」的文件名模板（出口分块？）

    publicPath: "/assets/", // string    // 输出解析文件的目录，url 相对于 HTML 页面

    library: "MyLibrary", // string,
    // 导出库(exported library)的名称

    libraryTarget: "umd", // 通用模块定义    // 导出库(exported library)的类型

    /* 高级输出配置（点击显示） */  },

  module: {
    // 关于模块配置
    rules: [
      // 模块规则（配置 loader、解析器等选项）

      {
        test: /\.jsx?$/,
        include: [
          path.resolve(__dirname, "app")
        ],
        exclude: [
          path.resolve(__dirname, "app/demo-files")
        ],
        // 这里是匹配条件，每个选项都接收一个正则表达式或字符串
        // test 和 include 具有相同的作用，都是必须匹配选项
        // exclude 是必不匹配选项（优先于 test 和 include）
        // 最佳实践：
        // - 只在 test 和 文件名匹配 中使用正则表达式
        // - 在 include 和 exclude 中使用绝对路径数组
        // - 尽量避免 exclude，更倾向于使用 include

        issuer: { test, include, exclude },
        // issuer 条件（导入源）

        enforce: "pre",
        enforce: "post",
        // 标识应用这些规则，即使规则覆盖（高级选项）

        loader: "babel-loader",
        // 应该应用的 loader，它相对上下文解析
        // 为了更清晰，`-loader` 后缀在 webpack 2 中不再是可选的
        // 查看 webpack 1 升级指南。

        options: {
          presets: ["es2015"]
        },
        // loader 的可选项
      },

      {
        test: /\.html$/,
        test: "\.html$"

        use: [
          // 应用多个 loader 和选项
          "htmllint-loader",
          {
            loader: "html-loader",
            options: {
              /* ... */
            }
          }
        ]
      },

      { oneOf: [ /* rules */ ] },
      // 只使用这些嵌套规则之一

      { rules: [ /* rules */ ] },
      // 使用所有这些嵌套规则（合并可用条件）

      { resource: { and: [ /* 条件 */ ] } },
      // 仅当所有条件都匹配时才匹配

      { resource: { or: [ /* 条件 */ ] } },
      { resource: [ /* 条件 */ ] },
      // 任意条件匹配时匹配（默认为数组）

      { resource: { not: /* 条件 */ } }
      // 条件不匹配时匹配
    ],

    /* 高级模块配置（点击展示） */  },

  resolve: {
    // 配置寻找模块的规则
    // （不适用于对 loader 解析）

    modules: [
      "node_modules",
      path.resolve(__dirname, "app")
    ],
    // 用于查找模块的目录

    extensions: [".js", ".json", ".jsx", ".css"],
    // 使用的扩展名

    alias: {
      // 模块别名列表

      "module": "new-module",
      // 起别名："module" -> "new-module" 和 "module/path/file" -> "new-module/path/file"

      "only-module$": "new-module",
      // 起别名 "only-module" -> "new-module"，但不匹配 "only-module/path/file" -> "new-module/path/file"

      "module": path.resolve(__dirname, "app/third/module.js"),
      // 起别名 "module" -> "./app/third/module.js" 和 "module/file" 会导致错误
      // 模块别名相对于当前上下文导入
    },
    /* 可供选择的别名语法（点击展示） */
    /* 高级解析选项（点击展示） */  },

  performance: { //输出文件的性能检查配置
    hints: "warning", // 枚举    maxAssetSize: 200000, // 整数类型（以字节为单位）
    maxEntrypointSize: 400000, // 整数类型（以字节为单位）
    assetFilter: function(assetFilename) {
      // 提供资源文件名的断言函数
      return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
    }
  },

  devtool: "source-map", // enum  // 通过在浏览器调试工具(browser devtools)中添加元信息(meta info)增强调试
  // 牺牲了构建速度的 `source-map' 是最详细的。

  context: __dirname, // string（绝对路径！）
  // webpack 的主目录
  // entry 和 module.rules.loader 选项
  // 相对于此目录解析

  target: "web", // 枚举  // 包(bundle)应该运行的环境，配置输出代码的运行环境
  // 更改 块加载行为(chunk loading behavior) 和 可用模块(available module)

  externals: ["react", /^@angular\//],  // 不要遵循/打包这些模块，而是在运行时从环境中请求他们

  stats: "errors-only",  // 精确控制要显示的 bundle 信息

  devServer: {
    proxy: { // 代理到后端服务接口
      '/api': 'http://localhost:3000'
    },
    contentBase: path.join(__dirname, 'public'), // boolean | string | array, 配置 DevServer HTTP 服务器的文件根目录
    compress: true, // 是否启用Gzip压缩
    historyApiFallback: true, // 是否开发 HTMLS History API 网页
    hot: true, // 是否开启模块热替换功能
    https: false, // 是否开启 HTTPS 模式
    noInfo: true, // only errors & warns on hot reload
    // ...
  },

  plugins: [
    // ...
  ],
  // 附加插件列表


  /* 高级配置（点击展示） */}
```

## 使用不同的语言进行配置


webpack 接受以多种编程和数据语言编写的配置文件。支持的文件扩展名列表，可以在 node-interpret 包中找到。使用 node-interpret，webpack 可以处理许多不同类型的配置文件。

[node-interpret](https://www.npmjs.com/package/interpret)

### TypeScript

为了用 TypeScript 书写 webpack 的配置文件，必须先安装相关依赖：

```
npm install --save-dev typescript ts-node @types/node @types/webpack
```

之后就可以使用 TypeScript 书写 webpack 的配置文件了：

```
import path from 'path';
import webpack from 'webpack';

console.log('**********TypeScript*********');
const config: webpack.Configuration = {
  mode: 'production',
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'index.bundle-ts.js'
  }
};

export default config;
```

以上示例假定 webpack 版本 >= 2.7，或者，在 tsconfig.json 文件中，具有 esModuleInterop 和 allowSyntheticDefaultImports 这两个新的编译器选项的较新版本 TypeScript。

[tsconfig.json配置](https://www.tslang.cn/docs/handbook/tsconfig-json.html)

注意，你还需要核对 tsconfig.json 文件。如果 tsconfig.json 中的 compilerOptions 中的 module 字段是 commonjs ，则配置是正确的，否则 webpack 将因为错误而构建失败。发生这种情况，是因为 ts-node 不支持 commonjs 以外的任何模块语法。

这个问题有两种解决方案：

* 修改 tsconfig.json。
* 安装 tsconfig-paths。

第一种方法：打开你的 tsconfig.json 文件并查找 compilerOptions。将 target 设置为 "ES5"，以及将 module 设置为 "CommonJS"（或者完全移除 module 选项）。

第二种方法：安装 tsconfig-paths 包：

```
npm install --save-dev tsconfig-paths
```

然后，为你的 webpack 配置，专门创建一个单独的 TypeScript 配置：

tsconfig-for-webpack-config.json

```
{
  "compilerOptions": {
    "module": "commonjs", //指定生成哪个模块系统代码
    "target": "es5" //指定ECMAScript目标版本
  }
}
```

> ts-node 可以使用 tsconfig-path 提供的环境变量来解析 tsconfig.json 文件。

然后，设置 tsconfig-path 提供的环境变量 process.env.TS_NODE_PROJECT，如下所示：

package.json

```
{
  "scripts": {
    "build": "TS_NODE_PROJECT=\"tsconfig-for-webpack-config.json\" webpack"
  }
}
```

### CoffeeScript
类似的，为了使用 CoffeeScript 来书写配置文件, 同样需要安装相关的依赖：

```
npm install --save-dev coffee-script
```

之后就可以使用 Coffeecript 书写配置文件了：

webpack.config.coffee

```
webpack = require('webpack')
path = require('path')

config =
  mode: 'production'
  entry: './src/index.js'
  output:
    path: path.resolve(__dirname, 'dist')
    filename: 'index.bundle.coffee.js'
  

module.exports = config
```

### Babel and JSX

在以下的例子中，使用了 JSX（React 形式的 javascript）以及 Babel 来创建 JSON 形式的 webpack 配置文件：

首先安装依赖：

```
npm install --save-dev babel-register jsxobj babel-preset-es2015
```

[jsxobj](https://www.npmjs.com/package/jsxobj)

.babelrc

```
{
    "presets": [ "es2015", "stage-3"],
    "plugins": [
		["transform-react-jsx", {"pragma":"h"}]
	]
}

```

webpack.config.babel.js

```
console.log('************JSX***********');
//import jsxobj from 'jsxobj';

function h(name, attrs, ...children) {
	let obj = { name, ...attrs };

	// invoke any functions, passing the object through them
	if (typeof name==='function') obj = name(obj);

	[].concat(...children).forEach( child => {
		// if the child is an object with a name property,
        // collapse it into the current object for MAGIC
        console.log(child);

		let { name } = child;
		if (name) {
			obj[name] = child;
			delete child.name;
		}
		else {
			obj.value = child;
		}
	});

	return obj;
}



// example of an import'd plugin
const CustomPlugin = config => ({
    ...config,
    name: 'custom-plugin'
});

const config = (
    <webpack target="web" watch mode="production">
        <entry path="./src/index.js"/>
        <resolve>
            <alias from="react" to="preact-compat"/>
            <alias from="react-dom" to="preact-compat"/>
        </resolve>
        {/* <plugins>
            <uglify-js
                opts={{
                    compression: true,
                    mangle: false
                }}/>
            <CustomPlugin foo="bar"/>
        </plugins> */}
    </webpack>
);

console.log(config);

export default config;

```


## 多种配置类型

除了导出单个配置对象，还有一些方式满足其他需求。

### 导出为一个函数

在大多数时候 ， 我们需要从同 一份源代码中构建出多份代码，例如一份用于开发 ， 一份用于发布到线上。

如果采用导出一个Object来描述Webpack所需配置的方法，则需要写两个文件， 一个用于开发环境， 一个用于线上环境。再在启动时通过 webpack --config webpack.config.js 指定使用哪个配置文件。

采用导出一个 Function 的方式 ， 能通过 JavaScript 灵活地控制配置，做到只用写一个配置文件就能完成以上要求。


作为导出一个配置对象的替代，还有一种可选的导出方式是，从 webpack 配置文件中导出一个函数。该函数在调用时，可传入两个参数：

* 环境对象(environment)作为第一个参数。有关语法示例，请查看CLI 文档的环境选项。 
* 一个选项 map 对象（argv）作为第二个参数。这个对象描述了传递给 webpack 的选项，并且具有 output-filename 和 optimize-minimize 等 key。

```
const path = require('path');
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

module.exports = function(env={}, argv) {
    const plugins = [];
    const isProduction = env['production'];

    // 在生成环境才压缩
    if (isProduction) {
        plugins.push(
         // 压缩输出的 JS 代码
         new UglifyJsPlugin());
    }

    let config = {
        plugins: plugins,
        entry: './src/index.js',
        mode: isProduction ? 'production' : 'development',
        // 在生成环境不输出 Source Map
        devtool: isProduction ? '' : 'source-map',
    };
  
    return config;
}
```

在运 行 Webpack 时，会向这个函数传入两个参数，如下所述。

* env:当前运行时的 Webpack 专属环境变量， env 是一个 Object。读取时直接访问 Object的属性，将它设置为需要在启动 Webpack时带上参数。例如启动命令是 webpack --env.production --env.bao=foo，则 env 的值是{"production": "true "，"bao" :"foo "}。

* argv:代表在启动 Webpack时通过命令行传入的所有参数，例如--config、--devtool，可以通过 webpack -h 列出所有Webpack支持的命令行参数。
就以上配置文件而言，在开发时执行命令webpack构建出方便调试的代码，在需要构建出发布到线上的代码时执行 webpack --env.production 构建出压缩的代码 。



### 导出一个Promise

webpack 将运行由配置文件导出的函数，并且等待 Promise 返回。便于需要异步地加载所需的配置变量。

```
module.exports = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({
                entry: './src/index.js',
                mode: 'production'
                /* ... */
            })
        }, 5000)
    })
}
```

### 导出多个配置对象
作为导出一个配置对象/配置函数的替代，你可能需要导出多个配置对象（从 webpack 3.1.0 开始支持导出多个函数）。当运行 webpack 时，所有的配置对象都会构建。例如，导出多个配置对象，对于针对多个构建目标（例如 AMD 和 CommonJS）打包一个 library 非常有用。

```
module.exports = [{
  output: {
    filename: './dist-amd.js',
    libraryTarget: 'amd'
  },
  entry: './app.js',
  mode: 'production',
}, {
  output: {
    filename: './dist-commonjs.js',
    libraryTarget: 'commonjs'
  },
  entry: './app.js',
  mode: 'production',
}]

```

## 入口和上下文

entry 对象是用于 webpack 查找启动并构建 bundle。其上下文是入口文件所处的目录的绝对路径的字符串。

### context
Webpack 在寻找相对路径的文件时会以 context 为根目录， context 默认为执 行启动 Webpack 时所在的当前工作目录。如果想改变 context 的默认配置，则可以在配置文件 里这 样设置它 :

```
context: path.resolve(__dirname, "app")
```

注意， context 必须是一个绝对路径的字符串。 除此之外，还可以通过在启动 Webpack
时带上参数 webpack --context 来设置 context。

### entry
entry 是配置模块的入口，可抽象成输入， Webpack 执行构建的第 一 步将从入 口开始， 搜寻及递归解析出所有入口依赖的模块。

e口try 配置是必填的，若不填则将导致 Webpack 报错、退出 。

```
string | [string] | object { <key>: string | [string] } | (function: () => 
string | [string] | object { <key>: string | [string] })
```
动态加载的模块不是入口起点。

简单规则：每个 HTML 页面都有一个入口起点。单页应用(SPA)：一个入口起点，多页应用(MPA)：多个入口起点。

```
entry: {
  home: "./home.js",
  about: "./about.js",
  contact: "./contact.js"
}
```

### 命名

Webpack会为每个生成的 Chunk取一个名称，Chunk的名称和Entry的配置有关。

* 如果 entry 是一个 string 或 array，就只会生成一个 Chunk，这时 Chunk 的名称是 main。
* 如果 entry 是一个 object，就可能会出现多个 Chunk，这时 Chunk 的名称是 object 键值对中键的名称。

### 动态入口
假如项目里有多个页面需要为每个页面的入口配置一个 Entry，但这些页面的数量可能会 不断增长，则这时 Entry的配置会受到其他因素的影响，导致不能写成静态的值 。其解决方法 是将 Entry设置成一个函数动态地返回上面所说的配置，代码如下:

```
// 同步函数 
entry: () => {
	return {
		a : ’./pages/a’, 
		b : ’./pages/b ’,
	}
}

//异步函数
entry: () => {
	return new Promise ((resolve)=>{
		resolve({
			a :’./pages/a ’, 
			b :’./pages/b ’,
		}); 
	}) ;
}
```










