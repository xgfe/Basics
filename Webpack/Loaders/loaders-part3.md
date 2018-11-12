	by yangxiaotong 2018.09

目录：

* istanbul-instrumenter-loader
* jshint-loader
* json-loader
* json5-loader
* bundle-loader
* mocha-loader
* multi-loader
* node-loader
* null-loader

##istanbul-instrumenter-loader

####作用
生成代码覆盖率报告
####安装
```npm i -D istanbul-instrumenter-loader```
###使用
一般和```karma-webpack```、```karma-coverage-istanbul-reporter```等插件一起使用
###例子
```
//karma.conf.js
// Karma configuration
// Generated on Wed Aug 08 2018 13:49:01 GMT+0800 (CST)

module.exports = function(config) {
  config.set({

    // base path that will be used to resolve all patterns (eg. files, exclude)
    basePath: '',


    // frameworks to use
    // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
    frameworks: ['jasmine'],


    // list of files / patterns to load in the browser
    files: [
      './src/**/**.spec.js'
    ],


    // list of files / patterns to exclude
    exclude: [
    ],


    // preprocess matching files before serving them to the browser
    // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor
    preprocessors: {
      './src/**/**.spec.js': ['webpack', 'sourcemap']
    },


    // test results reporter to use
    // possible values: 'dots', 'progress'
    // available reporters: https://npmjs.org/browse/keyword/karma-reporter
    reporters: ['coverage-istanbul'],


    // web server port
    port: 9876,


    // enable / disable colors in the output (reporters and logs)
    colors: true,

    webpack: {
      devtool: 'inline-source-map',
      module: {
        rules: [{
          test: /\.js/,
          use: {
            loader: 'istanbul-instrumenter-loader',
            options: {
              esModules: true
            }
          },
          enforce: 'post',
          exclude: /node_nodules|\.spec\.js$/
        }, {
          test: /\.spec.js$/,
          exclude: /(node_modules|bower_components)/,
          use: {
            loader: 'babel-loader',
            options: {
              presets: ['env'],
              plugins: ['istanbul']
            }
          }
          }]
      }
    },

    // level of logging
    // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
    logLevel: config.LOG_INFO,


    // enable / disable watching file and executing tests whenever any file changes
    autoWatch: true,


    // start these browsers
    // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
    browsers: ['Chrome'],

    plugins: [
      'karma-chrome-launcher',
      'karma-jasmine',
      // 'karma-requirejs',
      'karma-coverage-istanbul-reporter',
      'karma-webpack',
      'karma-sourcemap-loader'
    ],
    // Continuous Integration mode
    // if true, Karma captures browsers, runs the tests and exits
    singleRun: false,

    coverageIstanbulReporter: {
      // reports can be any that are listed here: https://github.com/istanbuljs/istanbuljs/tree/aae256fb8b9a3d19414dcf069c592e88712c32c6/packages/istanbul-reports/lib
      reports: ['html', 'text-summary'],

      // base output directory. If you include %browser% in the path it will be replaced with the karma browser name
      dir: './coverage',

      // Combines coverage information from multiple browsers into one report rather than outputting a report
      // for each browser.
      //combineBrowserReports: true,

      // if using webpack and pre-loaders, work around webpack breaking the source path
      //fixWebpackSourcePaths: true,

      // stop istanbul outputting messages like `File [${filename}] ignored, nothing could be mapped`
      //skipFilesWithNoCoverage: true,

      // Most reporters accept additional config options. You can pass these through the `report-config` option
      'report-config': {
        // all options available at: https://github.com/istanbuljs/istanbuljs/blob/aae256fb8b9a3d19414dcf069c592e88712c32c6/packages/istanbul-reports/lib/html/index.js#L135-L137
        html: {
          // outputs the report in ./coverage/html
          subdir: 'html'
        }
      },
    },
    // Concurrency level
    // how many browser should be started simultaneous
    concurrency: Infinity
  })
}
```##json-loader

###安装
```npm install --save-dev json-loader```

###作用

1. **由于 `webpack >= v2.0.0` 默认支持导入 `JSON` 文件，所以对于后缀名为`.json`的文件没必要再进行配置，如果配置的话会报错。**
2. 如果使用自定义文件扩展名，就需要配置`json-loader`。(文件后缀名可以是自定义的，但是内容必须是`json`格式)。

###使用方式
- 内联方式  
`const json = require('json-loader!./src/data.xxn');`

- 配置方式（报错） 
 
```
module.exports = {
    entry: './index.js',
    output: {
        filename: "index.js",
        path: path.join(__dirname, "dist")
    },

    module: {
        rules: [
            {
                test: /\.xxn$/,
                use: [
                  { loader: 'json-loader' }
              ]
            }
        ]
    }
}
```

##json5-loader
###json5

`JSON5`数据交换格式是`JSON`的超集，旨在通过扩展其语法以包括来自`ECMAScript 5.1`的一些产品来减轻`JSON`的一些限制。它的类型包括：`Objects``Arrays` `Strings` `Numbers` `Comments` `White Space`

###安装
`npm install --save-dev json5-loader`

###用法
首先，我们建了一个`json5`文件，如下：

```
{
  env: 'production',
  passwordStrength: 'strong'
}
``` 

- 配置方式

```
// webpack.config.js
const path = require('path');

module.exports = {
    entry: './index.js',
    output: {
        filename: "index.js",
        path: path.join(__dirname, "dist")
    },

    module: {
        rules: [
            {
                test: /\.json5$/,
                use: [
                  { loader: 'json5-loader' }
              ]
            }
        ]
    }
}
```

- 内联

```data = require("json5-loader!./src/sappData.json5")```

最后的输出都是```'production'```

##multi-loader
###作用
```webpack```使用```multi-loader```来分离模块。这个```loader```会加载这个模块多次，根据配置，每一次都会加载不同的```loader```。

###安装
```npm install multi-loader --save-dev```

###example

```
// webpack.config.js
const multi = require('multi-loader');
{
    module: {
        loaders: [
            {
                test: /\.css$/,
                // Add CSS to the DOM and return the raw content
                loader: multi(
                    'style-loader!css-loader',
                    'raw-loader'
                )
            }
        ]
    }
}
```

##null-loader
###作用
返回一个空的模块。它的一个用途是关闭依赖项导入的模块，例如，你的项目依赖于导入你不需要的polyfill的ES6库，因这里删除它将导致不会丢失功能。 测试polyfill的路径，它将不包含在你的包中。
我这里用一个例子表述一下：

```
//a.js
module.exports = function(val) {
    return val + ' sale';
}
```

```
//b.js
module.exports = function(val) {
    return val + ' bill';
}
```

```
//c.js
const a = require("./a");
const b = require('./b');

module.exports = {
    a: a,
    b: b
}
```

```
//index.js

const c = require('./src/c');

console.log(c.a('ye'));
console.log(c.b('ha'));
```

```
// webpack.config.js
const path = require('path');

module.exports = {
    entry: './index.js',
    mode: 'production',
    output: {
        filename: "index.js",//输出文件名，[name]表示入口文件js名
        path: path.join(__dirname, "dist")//输出文件路径
    },
    module: {
        rules: [
            {
                test: path.resolve(__dirname, './src/b.js'),
                use: 'null-loader'
            }
        ]
    }
}
```

最后输出会报错，因为当使用`null-loader`时，作为`c.js`的依赖导入项的`a.js`被关闭了，即输出为空模块。

##jshint-loader
###作用
`jshint-loader`用于在`js`文件构建的时候运行`jshint`(用于检查`javascript`代码错误和问题的工具).
###环境
`node`版本要大于`6.9.0 `，`webpack`版本要大于`4.0.0`
###安装
` npm install jshint-loader --save-dev`
###配置
```
const path = require('path');

module.exports = {
  entry: {
    app: './index.js'
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },

  module: {
    rules: [{
      test: /\.js$/, // include .js files
      enforce: "pre", // preload the jshint loader
      exclude: /node_modules/, // exclude any and all files in the node_modules folder
      use: [{
        loader: "jshint-loader",
        options: {
          camelcase: true,
          emitErrors: false,
          failOnHint: false
        }
      }]
    }]
  },
};
```

*注意：当我们运行`webpack`时会报错*：
![error]( /Users/yangxiaotong/Desktop/error.jpg)

原因：`Webpack4`在加载器上下文中删除了`this.options`，`jshint-loader`只是删除了代码。但是在`npm`注册表中还没有构建。

修改的方式：

方法一：找到`/node_modules/jshint-loader/index.js`文件并打开，将

```
function jsHint(input, options) {
    // copy options to own object
    if(this.options.jshint) {
        for(var name in this.options.jshint) {
            options[name] = this.options.jshint[name];
        }
    }
```
修改为：

```
function jsHint(input, options) {
	// copy options to own object
	if(this.options && this.options.jshint) {
        for(var name in this.options.jshint) {
            options[name] = this.options.jshint[name];
        }
    }
```
方法二：可以从`GitHub`中提取最新的`jshint-loader`，然后使用`npm link`
###配置
- emitErrors(默认值为`undefined`)

`jslint`的错误信息在默认情况下会显示为`warning`（警告）类信息. 

将`emitErrors`参数设置为`true`可使错误显示为`error`（错误）类信息.

- failOnHint(默认值为`undefined`)

`jshint`默认情况下不会打断`webpack`编译,如果想在`jshint`出现错误时，立刻停止编译,设置`failOnHint`参数为`true`

- reporter(默认值为`undefined`)
在默认情况下，`jshint-loader`会提供一个默认的报告方法,用于格式化和发出`JSHint`警告和错误

- Custom Reporter
你可以通过`reporter`配置自定义的报告方法.  
这个`reporter`函数会经过一个`jshint`的`error/warning`数组，它的结构如下：

```
[
{
    id:        [string, usually '(error)'],
    code:      [string, error/warning code],
    reason:    [string, error/warning message],
    evidence:  [string, a piece of code that generated this error]
    line:      [number]
    character: [number]
    scope:     [string, message scope;
                usually '(main)' unless the code was eval'ed]

    [+ a few other legacy fields that you don't need to worry about.]
},
// ...
// more errors/warnings
]
```
在这个`repoter`函数中，你可以通过`this.emitError(...)` `this.emitWarning(...)`来触发对应的函数。

**注意：`jshint`与`jshint-loader`相互不兼容，这是因为`reporter`函数的输入仅从一个文件处理而不是多个，以这种方式报告的错误与传统的`jshint`报告有所不同，它是针对每个源文件的，所以每个源文件都会有一个`repoter`报告**


##node-loader
###环境
`node`版本要大于`6.9.0 `，`webpack`版本要大于`4.0.0`
###example

```
//index.js
const index = require('./src/index.node');
```
```
//webpack.config.js
const path = require('path');

module.exports = {
    entry: './index.js',
    output: {
        filename: "./index.js",
        path: path.join(__dirname, "dist")
    },

    module: {
        rules: [
            {
                test: /\.node$/,
                use: [
                  { loader: 'node-loader' }
              ]
            }
        ]
    }
}
```
###使用方式
除了上述配置的方式，还可以

- cli命令  
`webpack --module-bind 'node=node-loader'`
- 内联
`const index = require('node-loader!./src/index.node');`


##mocha-loader
###作用
```mocha-loader```可以用于单元测试
###安装方法
```npm install mocha-loader --save-dev```
###配置
```
const path = require('path');

module.exports = {
    entry: './index.js',
    output: {
        filename: "index.js",
        path: path.join(__dirname, "dist")
    },
    module: {
        rules: [
            {
                test: /test\.js$/,
                use: 'mocha-loader',
                exclude: /node_modules/,
            }
        ]
    }
}
```
###example
```
//webpack.config.js
const path = require('path');

module.exports = {
    mode: 'production',
    entry: './index.js',
    output: {
        filename: "index.js",//输出文件名，[name]表示入口文件js名
        path: path.join(__dirname, "dist")//输出文件路径
    },
    module: {
        rules: [
            {
                test: /test\.js$/,
                use: 'mocha-loader',
                exclude: /node_modules/,
            }
        ]
    }
}
```

```
//a.js
module.exports = function(a, b) {
    return a + b;
};
```

```
//a.test.js
const assert = require("assert");
const add = require("./add");

describe("Demo", () => {
  it("should add correctly", () => {
    assert.equal(add(1, 1), 2);
  });
});
```

###使用方式
除了上面的配置方法，还可以使用：

- cli命令  
`webpack --module-bind 'mocha-loader!./tests/add.test.js'`
- 内联  
`const test = require('mocha-loader!./tests/add.test');`

##bundle-loader
###作用
按需加载的```loader```
###安装
```npm i bundle-loader --save```
###配置
```
const path = require('path');

module.exports = {
    entry: {
     index: './src/file.js'
    },
    output: {
      path: path.join(__dirname, "dest"),
      filename: '[name].js',
      chunkFilename: '[name].[id].js',
    },
    module: {
      rules: [
        {
          test: /\.bundle\.js$/,
          use: {
            loader: 'bundle-loader',
            options: {
              name: 'my-chunk'
            }
          }
        }
      ]
    }
  }
```
当`require`后缀名为`.bundle.js`的时候，这个`chunk`必须已经存在。

文件中的`file.js`文件：

```
const bundle = require('./file.bundle.js');
```
需要异步等待这个`chunk`可用并且获得了导出才会加载。
	
```	
bundle(() => {
    console.log(111);
});
bundle(function(file) {
  console.log(222);
  console.log(file());
});
```
这里的
`
bundle((file) => {
  ......
});
`中的`file`参数可以看作：
`const file = require('./file.js')`

其实是在`require.ensure`块中包含了`require(file.js)`

（关于`require.ensure`可以参考[require.ensure](http://www.css88.com/doc/webpack2/guides/code-splitting-require/)）

可以为`bundle`添加多个回调函数，并且这些回调函数会按顺序执行。如果在加载依赖关系后添加回调，它将立即被调用
###options
####lazy
如果想在调用的时候才加载,使用`lazy`属性可以在你调用这个`bundle`的时候才会去发起请求。

```
{
  loader: 'bundle-loader',
  options: {
    lazy: true
  }
}
```
####name
如果你想为`bundle`设置一个名字，就可以使用`name`选项。

```
//webpack.config.js
options: {
  name: 'my-chunk',
  lazy: false
}
```

**注意：```chunk```的名字会根据```output.chunkFilename```的规则来确定，默认值就是```[id].[name]```,这里的```name```就是```options```里设置的```name```**

这里是一个完整的配置：

```
const path = require('path');

module.exports = {
    entry: {
     index: './src/file.js'
    },
    output: {
      path: path.join(__dirname, "dest"),
      filename: '[name].js',
      chunkFilename: '[name].[id].js',
    },
    module: {
      rules: [
        {
          test: /\.bundle\.js$/,
          use: {
            loader: 'bundle-loader',
            options: {
              name: 'my-chunk',
              lazy: false
            }
          }
        }
      ]
    }
  }
```

一般的`chunks`会根据上述`filename`的规则来确定自己的`chunkname`,但是对于用`bundle-loader`处理过的`chunks`而言，它会根据`chunkFilename`的规则。所以上述例子会生成一个名叫`my-chunk.1.js`的文件。当然，在`chunkFilename`中也可以加入`hash`值，但是在`options`选项中加入`hash`值有可能不会生效。


###配置项

| Name | Type | Default | Description |
| ------ | ------ | ------ | ------|
| debug | {Boolean} | false | 转换为debug的模式 |
| compact | {Boolean} | true | 生成紧凑的代码 |
| autoWrap | {Boolean} | false | 允许函数外的return语句 |
| esModules | {Boolean} | false | 设置为es2015的模式 |
| coverageVariable | {String} | \_\_coverage\_\_ | 全局覆盖变量的名称 |
| preserveComments | {Boolean} | false | 在output中保留评论 |
| produceSourceMap | {Boolean} | false | 将生成已检测代码的源映射设置为true |
| sourceMapUrlCallback | {Function} | null | 在原始代码中找到源映射URL时调用的回调函数。使用源文件名和源映射URL时调用此函数 |