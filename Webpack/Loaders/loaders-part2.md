	by zhangjianfeng 2018.09

* eslint-loader
* exports-loader
* expose-loader
* extract-loader
* file-loader
* gzip-loader
* html-loader
* i18n-loader
* imports-loader


# eslint-loader
webpack的eslint加载器

## 安装
```javascript
npm install eslint-loader --save-dev
```
提示： 如果你还没有安装eslint，你也需要从npm安装eslint模块

```javascript
npm install eslint --save-dev
```
## 使用
在webpack配置文件中加入以下代码
```javascript
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
        options: {
          // eslint options (if necessary)
        }
      },
    ],
  },
  // ...
}
```
当使用转换器（例如babel-loader）的时候，
确保它们的顺序正确（自下而上），否则这些文件会在被babel-loader转换之后，再进行eslint检查。
```javascript
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: [
          "babel-loader",
          "eslint-loader",
        ],
      },
    ],
  },
  // ...
}
```
为了安全起见，你可以使用enforce: "pre" 来检查没有被其他loaders（例如babel-loader）修改过的源文件
```javascript
module.exports = {
  // ...
  module: {
    rules: [
      {
        enforce: "pre",
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "babel-loader",
      },
    ],
  },
  // ...
}
```
## 选项
可以使用标准的webpack loader配置项来配置[eslint](http://eslint.org/docs/developer-guide/nodejs-api#cliengine)的规则
请注意，你提供的配置选项将传递给CLIEngine。 这是一组不同于您在package.json或.eslintrc中指定的选项。 有关更多详细信息，请参阅[eslint文档](http://eslint.org/docs/developer-guide/nodejs-api#cliengine)的规则。

fix（default: false）
这个选项将启用[eslint 自动修复功能](http://eslint.org/docs/user-guide/command-line-interface#fix)
注意：如果某些问题无法正确修复，此选项可能会导致webpack进入无限构建循环。

cache（default: false）
此选项将启用将linting结果缓存到文件中。 这对于减少全量构建的lintinh时间特别有用。
这个选项可以是一个布尔值或者一个缓存路径地址（例如： './.eslint-loader-cache'）。
如果cache设置为true，缓存文件会被写入到./node_modules/.cache 目录，这是一个推荐写法。

formatter（default: eslint stylish formatter）

Loader接受一个具有一个参数的函数：一个eslint消息数组（object）。 该函数必须将输出作为字符串返回。 您可以使用官方的eslint格式化程序。
```javascript
module.exports = {
  entry: "...",
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
        options: {
          // several examples !

          // default value
          formatter: require("eslint/lib/formatters/stylish"),

          // community formatter
          formatter: require("eslint-friendly-formatter"),

          // custom formatter
          formatter: function(results) {
            // `results` format is available here
            // http://eslint.org/docs/developer-guide/nodejs-api.html#executeonfiles()

            // you should return a string
            // DO NOT USE console.*() directly !
            return "OUTPUT"
          }
        }
      },
    ],
  },
}
```

eslintPath (default: "eslint") 
将用于linting的eslint实例的路径。
如果eslintPath是官方eslint之类的文件夹，或者指定formatter选项。 现在你不需要安装eslint。

```javascript
module.exports = {
  entry: "...",
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
        options: {
          eslintPath: path.join(__dirname, "reusable-eslint"),
        }
      },
    ],
  },
  }
```

## 错误和警告
默认情况下，loader将根据eslint错误/警告计数自动调整错误报告。 您仍然可以使用emitError或emitWarning选项强制执行此行为：
emitError (default: false) 
如果此选项设置为true，则Loader将始终返回错误信息。

```javascript
module.exports = {
  entry: "...",
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
        options: {
          emitError: true,
        }
      },
    ],
  },
}
```
emitWarning (default: false)
如果option设置为true，Loader将始终返回警告信息。 如果您正在使用热模块更换，您可能希望在开发中启用此功能，否则在出现eslint错误时将跳过更新。

quiet (default: false) 
如果此选项设置为true，Loader将仅处理和报告错误，则忽略警告
```javascript
module.exports = {
  entry: "...",
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
        options: {
          quiet: true,
        }
      },
    ],
  },
}
```
failOnWarning (default: false) 

如果存在任何eslint警告，Loader将导致模块构建失败。

```javascript
module.exports = {
  entry: "...",
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
        options: {
          failOnWarning: true,
        }
      },
    ],
  },
}
```
failOnError (default: false) 

如果存在任何eslint错误，Loader将导致模块构建失败。
```javascript
module.exports = {
  entry: "...",
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
        options: {
          failOnError: true,
        }
      },
    ],
  },
}
```
outputReport (default: false) 

将错误的输出写入文件，例如checkstyle xml文件，用于报告Jenkins CI

这个文件路径是相对于webpack配置的output的path路径，你可以为输出文件使用一个不同的formatter，如果没有的话，将会使用默认配置的formatter
```javascript
module.exports = {
  entry: "...",
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: "eslint-loader",
        options: {
          outputReport: {
            filePath: 'checkstyle.xml',
            formatter: require('eslint/lib/formatters/checkstyle')
          }
        }
      },
    ],
  },
}
```
## 陷阱

### NoErrorsPlugin

无错误插件将会阻止webpack输出任何东西到打包文件，因此，即使ESLint警告也会使构建失败。无论eslint-loader使用什么错误设置。

因此，如果您希望在使用WebpackDevServer进行开发期间在控制台中看到ESLint警告，请从webpack配置中删除NoErrorsPlugin。

定义configFile或使用eslint -c path / .eslintrc

请记住，在定义configFile时，eslint不会自动在要被检测的文件的目录中查找.eslintrc文件。 有关详细信息，请参阅使用配置文件一节中的[官方eslint文档](https://eslint.org/docs/user-guide/configuring#using-configuration-files)。 见[＃129](https://github.com/webpack-contrib/eslint-loader/issues/129")。

# exports-loader
导出webpack的加载器模块

## 要求
This module requires a minimum of Node v6.9.0 and Webpack v4.0.0.

## 入门
安装exports-loader
```javascript
npm install exports-loader --save-dev
```
然后将loader添加到需要的require调用
```javascript
require('exports-loader?file,parse=helpers.parse!./file.js');
// adds the following code to the file's source:
//  exports['file'] = file;
//  exports['parse'] = helpers.parse;

require('exports-loader?file!./file.js');
// adds the following code to the file's source:
//  module.exports = file;

require('exports-loader?[name]!./file.js');
// adds the following code to the file's source:
//  module.exports = file;
```
然后运行webpack

# expose-loader
为webpack公开加载器模块
## 要求
This module requires a minimum of Node v6.9.0 and Webpack v4.0.0.

## 入门
安装expose-loader
```
$ npm install expose-loader --save-dev

```

将loader添加到配置文件，例如：
```javascript
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /.js/,
        use: [
          {
            loader: `expose-loader`,
            options: {...options}
          }
        ]
      }
    ]
  }
}

```

然后加载目标文件
```javascript
// src/entry.js
require("expose-loader?libraryName!./thing.js");
```

运行webpack

## 例子
比如，暴露jQuery出去一个全局变量$
```javascript
require("expose-loader?$!jquery");
```
因此，window.$在浏览器控制台就可用了

此外，你还可以在配置文件中设置

```javascript
// webpack.config.js
module: {
  rules: [{
    test: require.resolve('jquery'),
    use: [{
      loader: 'expose-loader',
      options: '$'
    }]
  }]
}
```
如果你想除了window.$还想暴露出window.jQuery，在loader字符串中你可以使用!
```javascript
// webpack.config.js
module: {
  rules: [{
    test: require.resolve('jquery'),
    use: [{
      loader: 'expose-loader',
      options: 'jQuery'
    },{
      loader: 'expose-loader',
      options: '$'
    }]
  }]
}

```

[require.resolve](https://nodejs.org/api/all.html#modules_require_resolve)调用是一个Node.js函数（与webpack处理中的require.resolve无关）。 require.resolve为您提供模块的绝对路径（“/.../app/node_modules/react/react.js”）。 因此，暴露仅适用于反应模块。 并且它仅在捆绑中使用时才会暴露。


# extract-loader
> 从bundle中提取HTML和CSS的webpack加载器

extract-loader动态评估给定的源代码并将结果作为字符串返回。 它的主要用例是解析来自各自加载器的HTML和CSS中的URL。 使用[file-loader](https://webpack.js.org/loaders/file-loader/)将extract-loader的结果作为单独的文件发出。
```javascript
import stylesheetUrl from "file-loader!extract-loader!css-loader!main.css";
// stylesheetUrl will now be the hashed url to the final stylesheet
```
extract-loader与[extract-text-webpack-plugin](https://webpack.js.org/plugins/extract-text-webpack-plugin/)的工作方式类似，是一种精简的替代方案。 在评估源代码时，它提供了一个虚假的上下文，它专门用于处理由html或[css-loader](https://webpack.js.org//loaders/css-loader/)生成的代码。 因此它可能在其他情况下不起作用。
## 安装
```javascript
npm install extract-loader
```

## 示例

将CSS与webpack捆绑在一起有一些很好的优点，例如在开发中使用散列网址或热模块替换引用图像和字体。 另一方面，在生产中，根据JS执行应用样式表并不是一个好主意。 渲染可能会延迟，甚至可能会出现FOUC。 因此，最好将它们作为最终生产版本中的单独文件。
使用extract-loader，您可以将main.css作为常规条目引用。 以下webpack.config.js显示了如何在开发中使用样式加载器加载样式，以及如何在生产中单独加载文件。

```javascript
const live = process.env.NODE_ENV === "production";
const mainCss = ["css-loader", path.join(__dirname, "app", "main.css")];

if (live) {
    mainCss.unshift("file-loader?name=[name].[ext]", "extract-loader");
} else {
    mainCss.unshift("style-loader");
}

module.exports = {
    entry: [
        path.join(__dirname, "app", "main.js"),
        mainCss.join("!")
    ],
    ...
};
```

你甚至可以将index.html添加为条目，并从那里引用样式表。 你只需要告诉html-loader也可以选择链接：href：

```javascript
const indexHtml = path.join(__dirname, "app", "index.html");

module.exports = {
    entry: [
        path.join(__dirname, "app", "main.js"),
        indexHtml
    ],
    ...
    module: {
        rules: [
            {
                test: indexHtml,
                use: [
                    {
                        loader: "file-loader",
                        options: {
                            name: "[name]-dist.[ext]",
                        },
                    },
                    {
                        loader: "extract-loader",
                    },
                    {
                        loader: "html-loader",
                        options: {
                            attrs: ["img:src", "link:href"],
                            interpolate: true,
                        },
                    },
                ],
            },
            {
                test: /\.css$/,
                loaders: [
                    {
                        loader: "file-loader",
                    },
                    {
                        loader: "extract-loader",
                    },
                    {
                        loader: "css-loader",
                    },
                ],
            },
            {
                test: /\.jpg$/,
                loaders: [
                    {
                        loader: "file-loader"
                    },
                ],
            },
        ]
    }
};

```
就会把下面代码
```javascript
<html>
<head>
    <link href="main.css" type="text/css" rel="stylesheet">
</head>
<body>
    <img src="hi.jpg">
</body>
</html>
```

转换成
```javascript
<html>
<head>
    <link href="7c57758b88216530ef48069c2a4c685a.css" type="text/css" rel="stylesheet">
</head>
<body>
    <img src="6ac05174ae9b62257ff3aa8be43cf828.jpg">
</body>
</html>

```

## 配置项
目前只有一个选项：publicPath。 如果您在webpack的输出选项中使用相对的publicPath并使用文件加载器解压缩到文件，则可能需要这样来考虑提取文件的位置。

```javascript
module.exports = {
    output: {
        path: path.resolve("./dist"),
        publicPath: "dist/"
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: "file-loader",
                        options: {
                            name: "assets/[name].[ext]",
                        },
                    },
                    {
                        loader: "extract-loader",
                        options: {
                            publicPath: "../",
                        }
                    },
                    {
                        loader: "css-loader",
                    },
                ],
            }
        ]
    }
};
```

# file-loader
> [![npm][npm]][npm-url] [![node][node]][node-url] [![deps][deps]][deps-url] [![tests][tests]][tests-url] [![chat][chat]][chat-url]

# 要求

Node v6.9.0以及webpack v3和webpack v4

# 开始

安装file-loader
```javascript
$ npm install file-loader --save-dev
```

Import (or require) the target file(s) in one of the bundle's files:

```javascript
// bundle file
import img from './file.png'
```

Then add the loader to your webpack config. For example:

```javascript
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {}
          }
        ]
      }
    ]
  }
}
```

通过首选的方法运行webpack。 这会将file.png作为文件放在输出目录中（如果指定了选项，则使用指定的命名约定）并返回文件的公共URI。

Note: 默认情况下，生成的文件的文件名是文件内容的MD5哈希值，其中包含所需资源的原始扩展名。

# Options

## context 
Type：String Default: context
```javascript
// webpack.config.js
{
  loader: 'file-loader',
  options: {
    name: '[path][name].[ext]',
    context: ''
  }
}
```
## emitFile
Type: Boolean Default: true

如果为true，则生成文件（将文件写入文件系统）。 如果为false，则加载程序将返回公共URI，但不会生成该文件。 对服务器端程序包禁用此选项通常很有用。

```javascript
// bundle file
import img from './file.png'
```

```javascript
// webpack.config.js
...
{
  loader: 'file-loader',
  options: {
    emitFile: false
  }
}
...
```

## name
Type: String|Function Default: '[hash].[ext]'

使用查询参数名称为目标文件指定自定义文件名模板。 例如，要将文件从上下文目录复制到保留完整目录结构的输出目录，您可以使用：
```javascript
// webpack.config.js
{
  loader: 'file-loader',
  options: {
    name: '[path][name].[ext]'
  }
}
```
或者使用一个函数：
```javascript
// webpack.config.js
...
{
  loader: 'file-loader',
  options: {
    name (file) {
      if (env === 'development') {
        return '[path][name].[ext]'
      }

      return '[hash].[ext]'
    }
  }
}
...
```

Note: 默认情况下，您指定的路径和名称将在同一目录中输出文件，并且还将使用相同的URI路径来访问该文件。

## outputPath
Type: String|Function Default: undefined

指定文件系统路径，其中将放置目标文件。
```javascript
// webpack.config.js
...
{
  loader: 'file-loader',
  options: {
    name: '[path][name].[ext]',
    outputPath: 'images/'
  }
}
...

```

## publicPath 
Type: String|Function Default: __webpack_public_path__

指定目标文件的自定义公共路径。

```javascript
// webpack.config.js
...
{
  loader: 'file-loader',
  options: {
    name: '[path][name].[ext]',
    publicPath: 'assets/'
  }
}
...
```

## regExp

Type: RegExp Default: undefined

```javascript
import img from './img1/file.png'
```
```javascript
{
  loader: 'file-loader',
  options: {
    regExp: /\/([a-z0-9]+)\/[a-z0-9]+\.jpg$/,
    name: '[1]-[name].[ext]'
  }
}
```
Note: 如果使用[0]，它将被整个测试字符串替换，而[1]将包含正则表达式的第一个捕获括号，依此类推......(正则捕获组的概念)


## useRelativePath

Type: Boolean Default: false

指定是否为每个目标文件上下文生成相对URI。

```javascript
// webpack.config.js
{
  loader: 'file-loader',
  options: {
    useRelativePath: process.env.NODE_ENV === "production"
  }
}

```

## Placeholders 

[ext] 
Type: String Default: file.extname

The file extension of the target file/resource.

[hash] 
Type: String Default: 'md5'

Specifies the hash method to use for hashing the file content. See [hash](https://github.com/webpack-contrib/file-loader#hashes).

[N] 
Type: String Default: undefined

The n-th match obtained from matching the current file name against the regExp

[name] 
Type: String Default: file.basename

The basename of the file/resource.

[path] 
Type: String Default: file.dirname

The path of the resource relative to the webpack/config context.

## Hashes
格式:
```
[<hashType>:hash:<digestType>:<length>]
```

## digestType
Type: String Default: 'hex'

哈希函数应该使用的摘要。有效值包括: base26, base32, base36, base49, base52, base58, base62, base64, and hex。

## hashType 
Type: String Default: 'md5'

has函数应该使用的散列类型。有效值包括: md5, sha1, sha256, and sha512。


## length 
Type: Number Default: 9999

用户还可以指定计算的散列的长度。


# gzip-loader
用于webpack的gzip加载器模块
启用加载gzip压缩资源。

## Install 
```javascript
npm install --save-dev gzip-loader

```

## Usage

```javascript
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.gz$/,
        enforce: 'pre',
        use: 'gzip-loader'
      }
    ]
  }
}
```
```javascript
require('./file.js');
```

# html-loader
将HTML导出为字符串。 当编译器要求时，HTML被最小化。

## Install

```javascript
npm i -D html-loader
```
## Usage 
默认情况下，每个本地的 ```<img src="image.png">``` 都需要通过 require （require('./image.png')）来进行加载。你可能需要在配置中为图片指定 loader（推荐 file-loader 或 url-loader ）
你可以通过查询参数 attrs，来指定哪个标签属性组合(tag-attribute combination)应该被此 loader 处理。传递数组或以空格分隔的 <tag>:<attribute> 组合的列表。（默认值：attrs=img:src）

如果使用自定义标签，并且其中许多使用了自定义属性，则不必指定每个标签属性，只需指定一个空标签，如attrs =：custom- src，它将匹配每个元素。
```javascript
{
  test: /\.(html)$/,
  use: {
    loader: 'html-loader',
    options: {
      attrs: [':data-src']
    }
  }
}
```
要完全禁用对标签属性的处理（例如，如果你在客户端处理图片加载），你可以传入 attrs=false。

## 示例
```javascript
{
  module: {
    rules: [
      { test: /\.jpg$/, use: [ "file-loader" ] },
      { test: /\.png$/, use: [ "url-loader?mimetype=image/png" ] }
    ]
  },
  output: {
    publicPath: "http://cdn.example.com/[hash]/"
  }
}

```
```javascript
<!-- file.html -->
<img src="image.png" data-src="image2x.png" >

```
```javascript
require("html-loader!./file.html");

// => '<img src="http://cdn.example.com/49eba9f/a992ca.png"
//         data-src="image2x.png">'

```

```javascript
require("html-loader?attrs=img:data-src!./file.html");

// => '<img src="image.png" data-src="data:image/png;base64,..." >'

```

```javascript
require("html-loader?attrs=img:src img:data-src!./file.html");
require("html-loader?attrs[]=img:src&attrs[]=img:data-src!./file.html");

// => '<img  src="http://cdn.example.com/49eba9f/a992ca.png"
//           data-src="data:image/png;base64,..." >'

```

```javascript
require("html-loader?-attrs!./file.html");

// => '<img  src="image.jpg"  data-src="image2x.png" >'

```

通过运行 webpack --optimize-minimize 来最小化
或者在 webpack.conf.js 的 rule 选项中指定 minimize 属性(打包的时候移除一些不必要的内容，比如注释等信息)


```javascript
module: {
  rules: [{
    test: /\.html$/,
    use: [ {
      loader: 'html-loader',
      options: {
        minimize: true
      }
    }],
  }]
}

```
默认情况下启用的最小化规则如下：
* removeComments
* removeCommentsFromCDATA
* removeCDATASectionsFromCDATA
* collapseWhitespace
* conservativeCollapse
* removeAttributeQuotes
* useShortDoctype
* keepClosingSlash
* minifyJS
* minifyCSS
* removeScriptTypeAttributes
* removeStyleTypeAttributes
可以使用webpack.conf.js中的以下选项禁用规则
```
module: {
  rules: [{
    test: /\.html$/,
    use: [ {
      loader: 'html-loader',
      options: {
        minimize: true,
        removeComments: false,
        collapseWhitespace: false
      }
    }],
  }]
}
```

对于以 / 开头的 url，默认行为是不转换它们。 如果设置了 root 查询参数，它将被添加到 URL 之前，然后进行转换。

和上面配置相同：
```
<img src="/image.jpg">

```

```
require("html-loader!./file.html");

// => '<img  src="/image.jpg">'
```

```
require("html-loader?root=.!./file.html");

// => '<img src="http://cdn.example.com/1d4226952694eee418ab/6ac05174ae9b62257ff3aa8be43cf828.jpg"  >'
```

## 插槽

你可以使用 interpolate 标记，为 ES6 模板字符串启用插值语法，就像这样：

```
require("html-loader?interpolate!./index.html");

```
```
<img src="${require(`./images/gallery.png`)}">

<div>${require('html-loader!./components/gallery.html')}</div>
```
## 导出格式
这里有几种不同的可用导出格式：

* module.exports（默认配置，cjs 格式）。"Hello world" 转为 module.exports = "Hello world";
* exports.default (当设置了 exportAsDefault 参数，es6to5 格式）。"Hello world" 转为 exports.default = "Hello world";
* export default (当设置了 exportAsEs6Default 参数，es6 格式)。"Hello world" 转为 export default "Hello world";

## 高级选项
如果你需要传递[更多高级选项](https://github.com/webpack/html-loader/pull/46)，特别是那些不能被字符串化，你还可以在 webpack.config.js 中定义一个 htmlLoader 属性：

```javascript
var path = require('path')

module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.html$/,
        use: [ "html-loader" ]
      }
    ]
  },
  htmlLoader: {
    ignoreCustomFragments: [/\{\{.*?}}/],
    root: path.resolve(__dirname, 'assets'),
    attrs: ['img:src', 'link:href']
  }
};
```
如果你需要定义两个不同的 loader 配置，你也可以通过 html-loader?config=otherHtmlLoaderConfig 改变配置的属性名：


```javascript
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.html$/,
        use: [ "html-loader?config=otherHtmlLoaderConfig" ]
      }
    ]
  },
  otherHtmlLoaderConfig: {
    ...
  }
};
```
## 导出到HTML文件
一个很常见的场景，将 HTML 导出到 .html 文件中，直接访问它们，而不是使用 javascript 注入。这可以通过3个 loader 的组合来实现：

* [file-loader](https://github.com/webpack/file-loader)
* [extract-loader](https://github.com/peerigon/extract-loader)
* [html-loader](https://webpack.docschina.org/loaders/html-loader)

html-loader 将解析 URL，并请求图片和你所期望的一切资源。extract-loader 会将 javascript 解析为合适的 html 文件，确保引用的图片指向正确的路径，file-loader 将结果写入 .html 文件。示例：

```
{
  test: /\.html$/,
  use: [ 'file-loader?name=[path][name].[ext]!extract-loader!html-loader' ]
}
```

# i18n-loader

## Usage
./colors.json
```javascript
{
    "red": "red",
    "green": "green",
    "blue": "blue"
}
```

./de-de.colors.json
```javascript
{
    "red": "rot",
    "green": "gr�n" 
}
```

## call it

```javascript
// assuming our locale is "de-de-berlin"

var locale = require("i18n!./colosrs.json");

// wait for ready, this is only required once for all locales in a web app
// because all locales of the same language are merged into once chuck
locale(function() {
    console.log(locale.red);// prints red 
    console.log(locale.blue);//prints blue
});
```

## options
如果想要一次加载然后可以同步地使用， 你应该告诉 loader 所有要使用的地区。

```javascript
{
  "i18n": {
    "locales": [
      "de",
      "de-de",
      "fr"
    ],
    // "bundleTogether": false
    // 可以禁止所有地区打包到一起
  }
}
```

## 可选的调用方法
```javascript
require("i18n/choose!./file.js"); // 根据地区选择正确的文件，
                    // 但是不会合并到对象中
require("i18n/concat!./file.js"); // 拼接所有合适的地区
require("i18n/merge!./file.js"); // 合并到对象中
                    // ./file.js 在编译时会被排除掉
require("i18n!./file.json") == require("i18n/merge!json!./file.json")
```
如果需要在 node 中使用，不要忘记填补（polyfill）require。 可以参考 webpack 文档。



# imports-loader
导入加载器允许您使用依赖于特定全局变量的模块。
这对于依赖全局变量（如$或者这是窗口对象）的第三方模块非常有用。 导入加载程序可以添加必要的require（'whatever'）调用，因此这些模块可以与webpack一起使用。

## 安装
```javascript
npm install imports-loader

```

## 用法
假设你有 example.js 这个文件
```javascript
$("img").doSomeAwesomeJqueryPluginStuff();

```

然后你可以像下面这样通过配置 imports-loader 插入 $ 变量到模块中：

```javascript
require("imports-loader?$=jquery!./example.js");

```
这将简单的把 var $ = require("jquery"); 前置插入到 example.js 中。

```javascript
Query value         Equals
angular             var angular = require("angular");
$=jquery            var $ = require("jquery");
define=>false       var define = false;
config=>{size:50}   var config = {size:50};
this=>window        function () { ... }).call(window);
```
## 多个值
使用逗号 , 来分隔和使用多个值：

```javascript
require("imports-loader?$=jquery,angular,config=>{size:50}!./file.js");

```

## webpack.config.js

同样的，在你的 webpack.config.js 配置文件中进行配置会更好：

```javascript
// ./webpack.config.js

module.exports = {
    ...
    module: {
        rules: [
            {
                test: require.resolve("some-module"),
                use: "imports-loader?this=>window"
            }
        ]
    }
};

```

## 典型的使用场景

### jQuery 插件
```javascript
imports-loader?$=jquery

```

### 自定义的 Angular 模块

```javascript
imports-loader?angular

```
### 禁用 AMD
有很多模块在使用 CommonJS 前会进行 define 函数的检查。自从 webpack 两种格式都可以使用后，在这种场景下默认使用了 AMD 可能会造成某些问题（如果接口的实现比较古怪）。

你可以像下面这样轻松的禁用 AMD

```javascript
imports-loader?define=>false

```



