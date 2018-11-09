目录：

* loaders
* babel-loader
* yaml-frontmatter-loader
* cache-loader
* coffee-loader
* coffee-redux-loader
* coverjs-loader
* css-loader



# loader
用法
第一种不通用
`var file = require("xxx-loader!./file.xxx");`

```
module: {
  rules: [
    {
      test: /\.xxx$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'xxx-loader',
        options: {
        }
      }
    }
  ]
}
```
##babel-loader
###描述
这个loader是让js文件可以通过babel进行转码
###安装
> webpack 3.x | babel-loader 8.x | babel 7.x
`npm install babel-loader@8.0.0-beta.0 @babel/core @babel/preset-env webpack`

> webpack 3.x babel-loader 7.x | babel 6.x
`npm install babel-loader babel-core babel-preset-env webpack`

###options

| option | default | 描述 |
| --- | --- | --- |
| ast | false | 返回对象里是否包含AST |
| auxiliaryCommentAfter | null | 在所有非用户注入代码之后附加注释 |
| auxiliaryCommentBefore | null | 在所有非用户注入代码之前附加注释 |
| root | . | 根目录相对路径，用于搜索babel.config.js .babelrc |
| configFile |  | 配置文件，默认是在（root）找babel.config.js |
| babelrc | true | 指定是否使用 .babelrc 和 babelignore 文件。使用 CLI 工具时不能使用该选项，请使用 --no-babelrc 代替。 |
| babelrcRoots | Root | babelrc的根目录 |
| envName | env vars | 默认使用的环境变量，可以设置BABEL_ENV 或者 NODE_ENV 不然就是 "development"|
| code | true |  启用代码生成选项。|
| comments | true | 在生成的代码中添加注释。 |
| compact | auto | 不要包含多余的空格符和换行符。设置为 "auto" 时，当输入大小 > 500KB 时，compact 会被设置为 true。 |
| cwd | . | Babel的编程选项加载的工作目录 |
| env | {} | 这是表示不同环境的键的对象。例如，当环境变量 BABEL_ENV 设置为 "production" 时，可以像这样设置 { env: { production: { /* specific options */ } } }。如果 BABEL_ENV 未设置，那么 NODE_ENV 将被启用，如果 NODE_ENV 也未被设置，则默认为 "development" 环境。|
| extends | null | 扩展 .babelrc 文件的路径 |
| filename | 'unknown' | 在错误信息是使用的文件名等。 |
| filenameRelative | (filename) | 相对于 sourceRoot 的文件名。 |
| generatorOpts | {} | 包含要传递给 babel 代码生成器(babel-generator)的选项对象。 |
| getModuleId | Null | 指定一个自定义回调来生成模块 ID 。调用方式为 getModuleId(moduleName)。如果返回值为 falsy ，则说明生成的模块 ID 被使用。 |
| highlightCode | true | ANSI 错误语法高亮显示。 |
| ignore | null | 与 only 选项相对。如果只指定 only，则 ignore 则被忽略。 |
| inputSourceMap | null | 输出的 source map 将基于该 source map 对象。 |
| minified | false | 保证输出最小化(不输出代码块最后一个分号，输出文字为字符串而不是转义字符串，安全情况下 new 后的 () 会被去除) |
| moduleId | null | 指定模块 ID 的自定义名称。|
| moduleIds | false | 如果值为 true，为模块添加一个明确的 ID 。默认情况下，所有模块都是匿名的。(不适用于 common 模块) |
| moduleRoot | (sourceRoot) | AMD 模块格式化程序的可选前缀，可以被预先添加到模块定义的文件名当中。 |
| only | null | 可填入一个 glob，正则表达式或者包含前者的混合数组，只编译匹配到的路径。也可以是包含明确匹配路径的数组。在尝试编译非匹配的文件时，它将原样返回。 |
| parserOpts | {} | 需要传递给 babel 解析器，babylon 的选项对象 |
| plugins | [] | 需要加载和使用的插件列表。 |
| presets | [] | 需要加载和使用的 presets (一组插件) 列表 |
| retainLines | false | 保留行号。这将导致代码变得很古怪，但对于不能使用 source map 的场景来说很方便。(注意: 这不会对列进行保留) |
| shouldPrintComment | null | 	一个可选的回调，控制是否需要输出注释。具体调用为 shouldPrintComment(commentContents) 。 注意: 该选项使用时会覆盖 comment 选项。 |
| sourceFileName | (filenameRelative) | 在返回的 source map 上设置 sources[0] |
| sourceMaps | false | 如果为 true ，添加一个 map 属性在输出的返回值中。如果设置为 "inline" ，带有 sourceMappingURL 指令的注释被添加到返回代码的底部。如果设置为 "both" ，则会返回 map 属性并追加 source map 注释。它不会自己生成 sourcemap 文件！ 要想让 CLI 生成 sourcemap ，你必须给它传递 --source-maps 选项。 |
| sourceRoot | (moduleRoot) | 所有 source 都是相对于 root 的。 |
| sourceType | 'module' | 设置 babel 解析代码的模式。可以设置为 “script” 或 “module” 。|
| wrapPluginVisitorMethod | null | 可用于包装访问者模式的可选回调。注意: 这对于自我检查这样的事是有必要的，并且不需要实现任何方法。具体调用为 wrapPluginVisitorMethod(pluginAlias, visitorType, callback) 。 |


##Yaml-frontmatter-Loader
###描述
将yaml文件转化为一个json对象，必须链式的使用json-loader
###用法

`var json = require("json-loader!yaml-frontmatter-loader!./file.md");`


```
module.exports = {
  module: {
    rules: [
      {
         test: /\.md$/,
         use: [ 'json-loader', 'yaml-frontmatter-loader' ]
      }
    ]
  }
}
```
##Cache-Loader
###安装
`npm install --save-dev cache-loader`

###option
|Name|Type|Default|Description|
|:--:|:--:|:-----:|:----------|
|**`cacheKey`**|`{Function(options, request) -> {String}}`|`undefined`|允许重写默认高速缓存密钥生成器|
|**`cacheDirectory`**|`{String}`|`path.resolve('.cache-loader')`|提供缓存目录，其中缓存项应该被存储（用于默认读/写实现）|
|**`cacheIdentifier`**|`{String}`|`cache-loader:{version} {process.env.NODE_ENV}`|提供用于生成散列的无效标识符。您可以使用它来实现加载器的额外依赖（用于默认读/写实现）。|
|**`write`**|`{Function(cacheKey, data, callback) -> {void}}`|`undefined`|允许您将默认写入缓存数据重写为文件（例如，ReDIS、MycCache）|
|**`read`**|`{Function(cacheKey, callback) -> {void}}`|`undefined`|允许您重写文件中的默认读取缓存数据|

####webpack.config.js

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: [
          'cache-loader',
          'babel-loader'
        ],
        include: path.resolve('src')
      }
    ]
  }
}
```

####webpack.config.js

```
// Or different database client - memcached, mongodb, ...
const redis = require('redis');
const crypto = require('crypto');

// ...
// connect to client
// ...

const BUILD_CACHE_TIMEOUT = 24 * 3600; // 1 day

function digest(str) {
  return crypto.createHash('md5').update(str).digest('hex');
}

// Generate own cache key
function cacheKey(options, request) {
  return `build:cache:${digest(request)}`;
}


// Read data from database and parse them
function read(key, callback) {
  client.get(key, (err, result) => {
    if (err) {
      return callback(err);
    }

    if (!result) {
      return callback(new Error(`Key ${key} not found`));
    }

    try {
      let data = JSON.parse(result);
      callback(null, data);
    } catch (e) {
      callback(e);
    }
  });
}


// Write data to database under cacheKey
function write(key, data, callback) {
  client.set(key, JSON.stringify(data), 'EX', BUILD_CACHE_TIMEOUT, callback);
}

module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: [
          {
            loader: 'cache-loader',
            options: {
              cacheKey,
              read,
              write,
            }
          },
          'babel-loader'
        ],
        include: path.resolve('src')
      }
    ]
  }
}
```

##Coffee-Loader
###描述
加载CoffeeScript
###安装
`npm install --save-dev coffee-loader`
###options

| Name | Default | 描述 |
| --- | --- | --- |
| literate | false | 是否支持md格式的CoffeeScript |
| sourceMap | false | 是否开启sourceMap |
| transpile | false | 提供babel的转码和插件 |

###example
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.coffee.md$/,
        use: [
          {
            loader: 'coffee-loader',
            options: { literate: true }
          }
        ]
      }
    ]
  }
}
```

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.coffee$/,
        use: [
          {
            loader: 'coffee-loader',
            options: { sourceMap: true }
          }
        ]
      }
    ]
  }
}
```

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.coffee$/,
        use: [
          {
            loader: 'coffee-loader',
            options: {
              transpile: {
                presets: ['env']
              }
            }
          }
        ]
      }
    ]
  }
}
```

##Coffee-Redux-loader
###安装
`npm i -D coffee-redux-loader`


##Coverjs-Loader
###用法

``` javascript
webpack-dev-server "mocha!./cover-my-client-tests.js" --options webpackOptions.js
```

``` javascript
// webpackOptions.js
module.exports = {
	// your webpack options
	output: "bundle.js",
	publicPrefix: "/",
	debug: true,
	includeFilenames: true,
	watch: true,

	// the coverjs loader binding
	postLoaders: [{
		test: "", // every file
		exclude: [
			"node_modules.chai",
			"node_modules.coverjs-loader",
			"node_modules.webpack.buildin"
		],
		loader: "coverjs-loader"
	}]
}
```

``` javascript
// cover-my-client-tests.js
require("./my-client-tests");

after(function() {
	require("cover-loader").reportHtml();
});
```
##Css-Loader

|Name|Type|Default|Description|
|:--:|:--:|:-----:|:----------|
|**[`url`](#url)**|`{Boolean}`|`true`| 启用/禁用 url() 处理|
|**[`import`](#import)** |`{Boolean}`|`true`| 启用/禁用 @import 处理|
|**[`modules`](#modules)**|`{Boolean}`|`false`|启用/禁用 CSS 模块|
|**[`localIdentName`](#localidentname)**|`{String}`|`[hash:base64]`|配置生成的标识符(ident)|
|**[`sourceMap`](#sourcemap)**|`{Boolean}`|`false`|启用/禁用 Sourcemap|
|**[`camelCase`](#camelcase)**|`{Boolean\|String}`|`false`|以驼峰化式命名导出类名|
|**[`importLoaders`](#importloaders)**|`{Number}`|`0`|在 css-loader 前应用的 loader 的数量|

### `url`

要禁用 `css-loader` 解析 `url()`，将选项设置为 `false`。

与现有的 css 文件兼容（如果不是在 CSS 模块模式下）。

```
url(image.png) => require('./image.png')
url(~module/image.png) => require('module/image.png')
```

### `import`

要禁用 css-loader 解析 `@import`，将选项设置为`false`

```css
@import url('https://fonts.googleapis.com/css?family=Roboto');
```

> _⚠️ Use with caution, since this disables resolving for **all** `@import`s, including css modules `composes: xxx from 'path/to/file.css'` feature._

### [`modules`](https://github.com/css-modules/css-modules)

查询参数 modules 会启用 CSS 模块规范。

默认情况下，这将启用局部作用域 CSS。（你可以使用 :global(...) 或 :global 关闭选择器 and/or 规则。

#### `Scope`

默认情况下，CSS 将所有的类名暴露到全局的选择器作用域中。样式可以在局部作用域中，避免全局作用域的样式。

语法 `:local(.className)` 可以被用来在局部作用域中声明 className。局部的作用域标识符会以模块形式暴露出去。

使用 `:local`（无括号）可以为此选择器启用局部模式。`:global(.className)` 可以用来声明一个明确的全局选择器。使用`:global`（无括号）可以将此选择器切换至全局模式。

loader 会用唯一的标识符(identifier)来替换局部选择器。所选择的唯一标识符以模块形式暴露出去。

```css
:local(.className) { background: red; }
:local .className { color: green; }
:local(.className .subClass) { color: green; }
:local .className .subClass :global(.global-class-name) { color: blue; }
```

```css
._23_aKvs-b8bW2Vg3fwHozO { background: red; }
._23_aKvs-b8bW2Vg3fwHozO { color: green; }
._23_aKvs-b8bW2Vg3fwHozO ._13LGdX8RMStbBE9w-t0gZ1 { color: green; }
._23_aKvs-b8bW2Vg3fwHozO ._13LGdX8RMStbBE9w-t0gZ1 .global-class-name { color: blue; }
```

> ℹ️ Identifiers are exported

```js
exports.locals = {
  className: '_23_aKvs-b8bW2Vg3fwHozO',
  subClass: '_13LGdX8RMStbBE9w-t0gZ1'
}
```

建议局部选择器使用驼峰式。它们在导入 JS 模块中更容易使用。
`url()` 中的 URL 在块作用域 (`:local .abc`) 规则中的表现，如同请求模块。

```
file.png => ./file.png
~module/file.png => module/file.png
```

你可以使用 `:local(#someId)`，但不推荐这种用法。推荐使用 class 代替 id。 你可以使用 localIdentName 查询参数（默认 [hash:base64]）来配置生成的 ident。

#### `Composing`

当声明一个局部类名时，你可以与另一个局部类名组合为一个局部类。

```css
:local(.className) {
  background: red;
  color: yellow;
}

:local(.subClass) {
  composes: className;
  background: blue;
}
```

这不会导致 CSS 本身的任何更改，而是导出多个类名。

```js
exports.locals = {
  className: '_23_aKvs-b8bW2Vg3fwHozO',
  subClass: '_13LGdX8RMStbBE9w-t0gZ1 _23_aKvs-b8bW2Vg3fwHozO'
}
```

``` css
._23_aKvs-b8bW2Vg3fwHozO {
  background: red;
  color: yellow;
}

._13LGdX8RMStbBE9w-t0gZ1 {
  background: blue;
}
```

#### `Importing`

从其他模块导入局部类名。

```css
:local(.continueButton) {
  composes: button from 'library/button.css';
  background: red;
}
```

```css
:local(.nameEdit) {
  composes: edit highlight from './edit.css';
  background: red;
}
```

要从多个模块导入，请使用多个 `composes:` 规则。

```css
:local(.className) {
  composes: edit hightlight from './edit.css';
  composes: button from 'module/button.css';
  composes: classFromThisModule;
  background: red;
}
```

####webpack.config.js
```js
{
  loader: 'css-loader',
  options: {
    modules: true,
    localIdentName: '[path][name]__[local]--[hash:base64:5]',
    getLocalIdent: (context, localIdentName, localName, options) => {
      return 'whatever_random_class_name'
    }
  }
}
```

> ℹ️ For prerendering with extract-text-webpack-plugin you should use `css-loader/locals` instead of `style-loader!css-loader` **in the prerendering bundle**. It doesn't embed CSS but only exports the identifier mappings.

### `sourceMap`

设置 `sourceMap` 选项查询参数来引入 source map。

例如 extract-text-webpack-plugin 能够处理它们。

默认情况下不启用它们，因为它们会导致运行时的额外开销，并增加了 bundle 大小 (JS source map 不会)。此外，相对路径是错误的，你需要使用包含服务器 URL 的绝对公用路径。

####webpack.config.js
```js
{
  loader: 'css-loader',
  options: {
    sourceMap: true
  }
}
```

### `camelCase`

默认情况下，导出 JSON 键值对形式的类名。如果想要驼峰化(camelize)类名（有助于在 JS 中使用），通过设置 css-loader 的查询参数 `camelCase` 即可实现。

|Name|Type|Description|
|:--:|:--:|:----------|
|**`true`**|`{Boolean}`|类名将被 camelized|
|**`'dashes'`**|`{String}`|只有类名中的破折号将被camelized|
|**`'only'`** |`{String}`|在 `0.27.1` 中加入。类名将被camelized，初始类名将从局部移除|
|**`'dashesOnly'`**|`{String}`|在 `0.27.1` 中加入。类名中的破折号将被camelized，初始类名将从局部移除|

####file.css
```css
.class-name {}
```

####file.js
```js
import { className } from 'file.css';
```

####webpack.config.js
```js
{
  loader: 'css-loader',
  options: {
    camelCase: true
  }
}
```

### `importLoaders`

查询参数 `importLoaders`，用于配置「`css-loader` 作用于 `@import `的资源之前」有多少个 loader。

####webpack.config.js
```js
{
  test: /\.css$/,
  use: [
    'style-loader',
    {
      loader: 'css-loader',
      options: {
        importLoaders: 2 // 0 => no loaders (default); 1 => postcss-loader; 2 => postcss-loader, sass-loader
      }
    },
    'postcss-loader',
    'sass-loader'
  ]
}
```

在模块系统（即 webpack）支持原始 loader 匹配后，此功能可能在将来会发生变化。

