	by zhouxiong 2018.12

* ExtractTextWebpackPlugin
* HashedModuleIdsPlugin
* HotModuleReplacementPlugin
* HtmlWebpackPlugin
* I18nWebpackPlugin
* IgnorePlugin


## ExtractTextWebpackPlugin插件

### 1. 用途

插件的用途是将CSS内容导出到单独的文件中。

### 2. 安装

```bash
npm install --save-dev extract-text-webpack-plugin
# 对于 webpack 2
npm install --save-dev extract-text-webpack-plugin@2.1.2
# 对于 webpack 1
npm install --save-dev extract-text-webpack-plugin@1.0.1
```

### 3. 用法

```js
const ExtractTextPlugin = require("extract-text-webpack-plugin");

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: "style-loader",
          use: "css-loader"
        })
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin("styles.css"),
  ]
}
```

它会将所有的入口 chunk(entry chunks)中引用的 `*.css`，移动到独立分离的 CSS 文件。因此，你的样式将不再内嵌到 JS bundle 中，而是会放到一个单独的 CSS 文件（即 `styles.css`）当中。 如果你的样式文件大小较大，这会做更快提前加载，因为 CSS bundle 会跟 JS bundle 并行加载。

| 优点                                                         | 缺点                              |
| ------------------------------------------------------------ | --------------------------------- |
| 更少 style 标签 (旧版本的 IE 浏览器有限制)                   | 额外的 HTTP 请求                  |
| CSS SourceMap (使用 `devtool: "source-map"` 和 `extract-text-webpack-plugin?sourceMap` 配置) | 更长的编译时间                    |
| CSS 请求并行                                                 | 没有运行时(runtime)的公共路径修改 |
| CSS 单独缓存                                                 | 没有热替换                        |
| 更快的浏览器运行时(runtime) (更少代码和 DOM 操作)            | ...                               |

### 4. 选项

```js
new ExtractTextPlugin(options: filename | object)
```

| 名称            | 类型        | 描述                                                         |      |
| --------------- | ----------- | ------------------------------------------------------------ | ---- |
| **id**          | `{String}`  | 此插件实例的唯一 ident。（仅限高级用途，默认情况下自动生成） |      |
| **filename**    | `{String}`  | 生成文件的文件名。可能包含 `[name]`, `[id]`和`[contenthash]` |      |
| **allChunks**   | `{Boolean}` | 从所有额外的 chunk(additional chunk) 提取（默认情况下，它仅从初始chunk(initial chunk) 中提取） 当使用 `CommonsChunkPlugin` 并且在公共 chunk 中有提取的 chunk（来自`ExtractTextPlugin.extract`）时，`allChunks` 必须设置为 `true` |      |
| **disable**     | `{Boolean}` | 禁用插件                                                     |      |
| **ignoreOrder** | `{Boolean}` | 禁用顺序检查 (这对 CSS 模块很有用！)，默认 `false`           |      |

> :警告: `ExtractTextPlugin` 对 **每个入口 chunk** 都生成一个对应的文件，所以当你配置多个入口 chunk 的时候，你必须使用 `[name]`, `[id]` 或 `[contenthash]`，

#### `#extract`

```js
ExtractTextPlugin.extract(options: loader | object)
```

从一个已存在的 loader 中，创建一个提取(extract) loader。支持的 loader 类型 `{ loader: [name]-loader -> {String}, options: {} -> {Object} }`。

| 名称                   | 类型                            | 描述                                                         |
| ---------------------- | ------------------------------- | ------------------------------------------------------------ |
| **options.use**        | `{String}`/`{Array}`/`{Object}` | loader 被用于将资源转换成一个 CSS 导出模块 *(必填)*          |
| **options.fallback**   | `{String}`/`{Array}`/`{Object}` | loader（例如 `'style-loader'`）应用于当 CSS 没有被提取(也就是一个额外的 chunk，当 `allChunks: false`) |
| **options.publicPath** | `{String}`                      | 重写此 loader 的 `publicPath` 配置                           |

#### 多个实例

如果有多于一个 `ExtractTextPlugin` 示例的情形，请使用此方法每个实例上的 `extract` 方法。

```js
const ExtractTextPlugin = require('extract-text-webpack-plugin');

// 创建多个实例
const extractCSS = new ExtractTextPlugin('stylesheets/[name]-one.css');
const extractLESS = new ExtractTextPlugin('stylesheets/[name]-two.css');

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: extractCSS.extract([ 'css-loader', 'postcss-loader' ])
      },
      {
        test: /\.less$/i,
        use: extractLESS.extract([ 'css-loader', 'less-loader' ])
      },
    ]
  },
  plugins: [
    extractCSS,
    extractLESS
  ]
};
```

### 提取 Sass 或 LESS

配置和上面是相同的，需要时可以将 `sass-loader` 切换为 `less-loader`。

```js
const ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: ['css-loader', 'sass-loader']
        })
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin('style.css')
  ]
}
```

### 修改文件名

`filename` 参数可以是 `Function`。它通过 `getPath` 来处理格式，如 `css/[name].css`，并返回真实的文件名，你可以用 `css` 替换 `css/js`，你会得到新的路径 `css/a.css`。

```js
entry: {
  'js/a': "./a"
},
plugins: [
  new ExtractTextPlugin({
    filename:  (getPath) => {
      return getPath('css/[name].css').replace('css/js', 'css');
    },
    allChunks: true
  })
]
```

## HashedModuleIdsPlugin

该插件会根据模块的相对路径生成一个四位数的hash作为模块id, 建议用于生产环境。

```js
new webpack.HashedModuleIdsPlugin({
  // 参数...
})
```

### 1. 参数

该插件支持以下参数：

- `hashFunction`: 散列算法，默认为 'md5'。支持 Node.JS [`crypto.createHash`](https://nodejs.org/api/crypto.html#crypto_crypto_createhash_algorithm_options) 的所有功能。
- `hashDigest`: 在生成 hash 时使用的编码方式，默认为 'base64'。支持 Node.js [`hash.digest`](https://nodejs.org/api/crypto.html#crypto_hash_digest_encoding) 的所有编码。
- `hashDigestLength`: 散列摘要的前缀长度，默认为 4。

### 2. 用法

下面是使用该插件的例子：

```js
new webpack.HashedModuleIdsPlugin({
  hashFunction: 'sha256',
  hashDigest: 'hex',
  hashDigestLength: 20
})
```

## HotModuleReplacementPlugin

启用热替换模块(Hot Module Replacement)，也被称为 HMR。

注意：**永远不要**在生产环境(production)下启用 HMR。

### 1. 基本用法(Basic Usage)

启用 HMR 非常简单，在大多数情况下也不需要设置选项。

```javascript
new webpack.HotModuleReplacementPlugin({
  // Options...
})
```

### 2. 选项(Options)

包含如下选项：

- `multiStep` (boolean)：设置为 `true` 时，插件会分成两步构建文件。首先编译热加载 chunks，之后再编译剩余的通常的资源。
- `fullBuildTimeout` (number)：当 `multiStep` 启用时，表示两步构建之间的延时。
- `requestTimeout` (number)：下载 manifest 的延时（webpack 3.0.0 后的版本支持）。

注意：这些选项属于实验性内容，因此以后可能会被弃用。就如同上文所说的那样，这些选项通常情况下都是没有必要设置的，仅仅是设置一下 `new webpack.HotModuleReplacementPlugin()` 在大部分情况下就足够了。

## HtmlWebpackPlugin

`HtmlWebpackPlugin`简化了HTML文件的创建，以便为你的webpack包提供服务。这对于在文件名中包含每次会随着编译而发生变化哈希的 webpack bundle 尤其有用。 你可以让插件为你生成一个HTML文件，使用[lodash模板](https://lodash.com/docs#template)提供你自己的模板，或使用你自己的[loader](https://webpack.css88.com/loaders)。

### 1. 安装

```bash
npm install --save-dev html-webpack-plugin
```

### 2. 基本用法

该插件将为你生成一个 HTML5 文件， 其中包括使用 `script` 标签的 body 中的所有 webpack 包。 只需添加插件到你的 webpack 配置如下：

```javascript
var HtmlWebpackPlugin = require('html-webpack-plugin');
var path = require('path');

var webpackConfig = {
  entry: 'index.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'index_bundle.js'
  },
  plugins: [new HtmlWebpackPlugin()]
};
```

这将会产生一个包含以下内容的文件 `dist/index.html`：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>webpack App</title>
  </head>
  <body>
    <script src="index_bundle.js"></script>
  </body>
</html>
```

如果你有多个 webpack 入口点， 他们都会在生成的HTML文件中的 `script` 标签内。

如果你有任何CSS assets 在webpack的输出中（例如， 利用[ExtractTextPlugin](https://webpack.css88.com/plugins/extract-text-webpack-plugin)提取CSS）， 那么这些将被包含在HTML head中的`<link>`标签内。

### 3. 配置

获取所有的配置选项。

| Name                   | Type                        | Default                                              | Description                                                  |
| ---------------------- | --------------------------- | ---------------------------------------------------- | ------------------------------------------------------------ |
| **title**              | `{String}`                  | `Webpack App`                                        | 生成的HTML文档的标题                                         |
| **filename**           | `{String}`                  | `index.html`                                         | 生成的文件名，可以设置成子目录中的任意文件名，例如assets/admin.html |
| **template**           | `{String}`                  | ``                                                   | 自定义的文件模板                                             |
| **templateParameters** | `{Boolean|Object|Function}` | ``                                                   | 在template中允许复写一些参数                                 |
| **inject**             | `{Boolean|String}`          | `true`                                               | 注入的脚本的位置，可选项为true \|\| head \|\| body \|\| false。当是true或者body时，脚本会被添加至body元素的底部，如果为head，会被添加至head元素中。 |
| **favicon**            | `{String}`                  | ``                                                   | 增加一个favicon图标                                          |
| **meta**               | `{Object}`                  | `{}`                                                 | 允许注入 `meta`标签， 例如 `meta: {viewport: 'width=device-width, initial-scale=1, shrink-to-fit=no'}` |
| **minify**             | `{Boolean|Object}`          | `true` if `mode`is `'production'`, otherwise `false` | 在生产环境中，设置为true，其他环境中都设置为false。          |
| **hash**               | `{Boolean}`                 | `false`                                              | 如果使用true，则会针对每个script和css文件生成一个hash值，防止缓存 |
| **cache**              | `{Boolean}`                 | `true`                                               | 缓存相关，只有在文件改变时，才会使用新的文件                 |
| **showErrors**         | `{Boolean}`                 | `true`                                               | 错误详情会被写至HTML文件中                                   |
| **chunks**             | `{?}`                       | `?`                                                  | 允许新增一些块文件，例如单元测试                             |
| **chunksSortMode**     | `{String|Function}`         | `auto`                                               | 设置新增的块文件的排序方式，可选值为`'none' | 'auto' | 'dependency' | 'manual' | {Function}` |
| **excludeChunks**      | `{Array.<string>}`          | ``                                                   | 忽略掉一些块文件，例如不添加单元测试块                       |
| **xhtml**              | `{Boolean}`                 | `false`                                              | 在XHTML中，如果为true，那么link标签需要正确的关闭。          |

## I18nWebpackPlugin

一个用于处理国际化的webpack插件

### 1. 安装

```bash
npm install i18n-webpack-plugin --save-dev
```

### 2. 用法

```js
// webpack.config.js
const I18nPlugin = require('i18n-webpack-plugin');
module.exports = {
  entry: //...,
  output: //...,
  plugins: [
    new I18nPlugin(languageConfig, optionsObj)
  ]
}
```

### 3. 选项

#### languageConfig

languageConfig表示的是程序提供的所有的国际化语言处理文件。

#### optionsObj

- `functionName`:  默认值为`__`，可以手动修改
- `failOnMissing`: 当匹配不到值时的处理方式，如果是false，则会给出一段提示信息，如果是true，则会抛出错误信息，默认值为false；
- `hideMessage`: 是否隐藏警告/错误提示信息，如果设置为true，则会隐藏，默认值为false；
- `nested`: 默认值为false，只有在languageConfig不为一个function时，才可以设置为true，表示languageConfig中的key是可以嵌套的。

## IgnorePlugin

防止在 `import` 或 `require` 调用第三方npm包时，生成以下正则表达式匹配的模块：

- `requestRegExp`  匹配资源请求路径的正则表达式。
- `contextRegExp` （可选）资源上下文（目录）的正则表达式。

```js
new webpack.IgnorePlugin(requestRegExp, [contextRegExp])
```

即一些不必要的文件，在我们引入后可以不用打包，这样可以减少webpack生成的output文件的大小。

以下示例演示了此插件的用法。

### 1. 忽略 moment 的本地化内容

以moment包使用为例，默认的moment包在require时，会将全部的locale文件全部打包，导致打包后的文件很大，其中向本地化的内容其实可以不用打包，只需要将核心功能打包即可。

因此我们可使用 `IgnorePlugin`在打包时忽略本地化内容:

```js
new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)
```