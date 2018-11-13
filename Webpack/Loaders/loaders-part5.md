	by lihaijie 2018.11

* svg-inline-loader
* thread-loader
* transform-loader
* url-loader
* val-loader
* worker-loader
* less-loader

# webpack loaders
##一、svg-inline-loader
当使用Adobe suite 或者 Sketch 进行导出svg时候，会带出多余的标签信息，该loader会将这些多余信息去掉

###安装
```
npm install svg-inline-loader --save-dev
```
###配置
```
//简单配置
{
    test: /\.svg$/,
    loader: 'svg-inline-loader'
}
```

###Query选项
####1、removingTags  
删除指定标签和它的子标签

```
removingTags: [...string]
该属性值只有当removeTags:true时候才会执行


默认值:removeTags:false
removingTags: ['title', 'desc', 'defs', 'style']

```
####2、warnTags
提醒标签

```
warnTags: [...string]
default: warnTags: []
```
####3、removeSVGTagAttrs  removingTagAttrs
移除svg属性

```
默认值: removeSVGTagAttrs: true
删除 <svg /> 的 width 和 height 属性。

removingTagAttrs: [...string]  删除svg内部属性
默认值: removingTagAttrs: []
```

####4、warnTagAttrs
在console发出关于内部`<svg/>`属性的警告

```
默认值: warnTagAttrs: []
```
####5、classPrefix
添加一个前缀到svg文件的class，以避免碰撞,默认值false

```
classPrefix: boolean || string
```

####6、idPrefix
添加一个前缀到svg文件的id，以避免碰撞,默认值false

```
idPrefix: boolean || string
```

##二、thread-loader
用一个单独的线程池来运行loaders,可以提高运行效率，减少编译时间
###安装
```
npm install --save-dev thread-loader
```
###使用限制
```
1、这些 loader 不能产生新的文件。
2、这些 loader 不能使用定制的 loader API（也就是说，通过插件）。
3、这些 loader 无法获取 webpack 的选项设置。

每个 worker 都是一个单独的有 600ms 限制的 node.js 进程。同时跨进程的数据交换也会被限制。
```
实例

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        include: path.resolve("src"),
        use: [
          "thread-loader",
          "expensive-loader"
        ]
      }
    ]
  }
}
```

配置项

```
use: [
  {
    loader: "thread-loader",
    // 有同样配置的 loader 会共享一个 worker 池(worker pool)
    options: {
      // 产生的 worker 的数量，默认是 cpu 的核心数
      workers: 2,

      // 一个 worker 进程中并行执行工作的数量
      // 默认为 20
      workerParallelJobs: 50,

      // 额外的 node.js 参数
      workerNodeArgs: ['--max-old-space-size', '1024'],

      // 闲置时定时删除 worker 进程
      // 默认为 500ms
      // 可以设置为无穷大， 这样在监视模式(--watch)下可以保持 worker 持续存在
      poolTimeout: 2000,

      // 池(pool)分配给 worker 的工作数量
      // 默认为 200
      // 降低这个数值会降低总体的效率，但是会提升工作分布更均一
      poolParallelJobs: 50,

      // 池(pool)的名称
      // 可以修改名称来创建其余选项都一样的池(pool)
      name: "my-pool"
    }
  },
  "expensive-loader"
]
```

预热
可以通过预热 worker 池(worker pool)来防止启动 worker 时的高延时。

这会启动池(pool)内最大数量的 worker 并把指定的模块载入 node.js 的模块缓存中。

```
const threadLoader = require('thread-loader');

threadLoader.warmup({
  // pool options, like passed to loader options
  // must match loader options to boot the correct pool
}, [
  // modules to load
  // can be any module, i. e.
  'babel-loader',
  'babel-preset-es2015',
  'sass-loader',
]);
```


##三、url-loader
url-loader 功能类似于 file-loader，但是在文件大小（单位 byte）低于指定的限制时，可以返回一个 DataURL。
###安装

```
npm install --save-dev url-loader
```
###用法

```
import img from './image.png'



module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192
            }
          }
        ]
      }
    ]
  }
}

```

###参数

```
limit:限制转换为dataUrl的大小，超过该大小即不转换
mimetype:设置文件的MIME type
fallback:当文件大小大于limit时候所使用的loader


{
  loader: 'url-loader',
  options: {
    limit: 8192
  }
}

{
  loader: 'url-loader',
  options: {
    mimetype: 'image/png'
  }
}

{
  loader: 'url-loader',
  options: {
    fallback: 'responsive-loader'
  }
}

```

##四、val-loader
可以在buid时候动态执行所加载模块的代码

###安装

```
$ npm install val-loader --save-dev
```
###用法
此 loader 所加载的模块必须符合以下接口

```
加载的模块必须使用以下函数接口，将 default export 导出为一个函数。
module.exports = function () {...};

export default function () {...};
```

###返回对象属性
```
1、code(string/Buffer)：The code that is passed to the next loader or to webpack
2、sourceMap（obj）:Will be passed to the next loader or to webpack
3、ast(arr):将传给下个loader的抽象语法树，对加速构建有用
4、dependencies(arr):需要监听变化的依赖相对、绝对路径文件(文件)
5、contextDependencies(arr):需要监听变化的依赖相对、绝对路径文件夹
6、cacheable：如果dependencies不变，在watch模式下，是否可复用代码
```
## 五、worker-loader

### 安装
```
npm install worker-loader --save-dev
```
#### Inlined
```
import Worker from 'worker-loader!./Worker.js';
```
#### Config
```
// webpack.config.js
{
  module: {
    rules: [
      {
        test: /\.worker\.js$/,
        use: { loader: 'worker-loader' }
      }
    ]
  }
}


// App.js
import Worker from './file.worker.js';

const worker = new Worker();

worker.postMessage({ a: 1 });
worker.onmessage = function (event) {};

worker.addEventListener("message", function (event) {});
```

#### Options
```
1、fallback 如果环境不支持worker的fallback
// webpack.config.js
{
  loader: 'worker-loader'
  options: { fallback: false }
}
```

```
2、inline 
Web workers without a separate Javascript file

// webpack.config.js
{
  loader: 'worker-loader',
  options: { inline: true }
}
```

```
3、name 
对输出的script文件起一个名字

// webpack.config.js
{
  loader: 'worker-loader',
  options: { name: 'WorkerName.[hash].js' }
}
```

```
4、publicPath 

重写覆盖worker scripts的下载路径，如果未设置，则用其它webpack assets的路径

// webpack.config.js
{
  loader: 'worker-loader'
  options: { publicPath: '/scripts/workers/' }
}
```

##六、less-loader
一个将less转换为css的loader

###安装
```
npm install less-loader --save-dev


//less-loader需要less作为前置依赖模块
// webpack.config.js
module.exports = {
  ...
  module: {
    rules: [{
      test: /\.less$/,
      loader: 'less-loader' // compiles Less to CSS
    }]
  }
};
```

###example
```
// webpack.config.js
module.exports = {
  ...
  module: {
    rules: [{
      test: /\.less$/,
      use: [{
        loader: 'style-loader' // creates style nodes from JS strings
      }, {
        loader: 'css-loader' // translates CSS into CommonJS
      }, {
        loader: 'less-loader' // compiles Less to CSS
      }]
    }]
  }
};
```
在生产环境中，一般配合MiniCssExtractPlugin进行css的文件抽离

###usage
webpack 提供了一种解析文件的高级机制。less-loader 应用一个 Less 插件，并且将所有查询参数传递给 webpack resolver。所以，你可以从 node_modules 导入你的 less 模块。只要加一个 ~ 前缀，告诉 webpack 去查询模块。

```
@import "~bootstrap/less/bootstrap";
```

使用 webpack resolver，你可以引入任何文件类型。你只需要一个导出有效的 Less 代码的 loader。通常，你还需要设置 issuer 条件，以确保此规则仅适用于 Less 文件的导入

```
// webpack.config.js
module.exports = {
    ...
    module: {
        rules: [{
            test: /\.js$/,
            issuer: /\.less$/,
            use: [{
                loader: "js-to-less-loader"
            }]
        }]
    }
};
```

如果使用path参数，将不使用 webpack's resolver ,path使用绝对路径

```
// webpack.config.js
module.exports = {
    ...
    module: {
        rules: [{
            test: /\.less$/,
            use: [{
                loader: "style-loader"
            }, {
                loader: "css-loader"
            }, {
                loader: "less-loader", options: {
                    paths: [
                        path.resolve(__dirname, "node_modules")
                    ]
                }
            }]
        }]
    }
};
```

####使用插件

```
// webpack.config.js
const CleanCSSPlugin = require("less-plugin-clean-css");

module.exports = {
            {
                loader: "less-loader", options: {
                    plugins: [
                        new CleanCSSPlugin({ advanced: true })
                    ]
                }
            }]
};
```

####启用sorce Map,在css-loader和less-loader处设置
```
module.exports = {
    ...
    module: {
        rules: [{
            test: /\.less$/,
            use: [{
                loader: "style-loader"
            }, {
                loader: "css-loader", options: {
                    sourceMap: true
                }
            }, {
                loader: "less-loader", options: {
                    sourceMap: true
                }
            }]
        }]
    }
};
```


##六、transform-loader
使用browserify 变换作为webpack-loader。
Browserify 可以让你使用类似于 node 的 require() 的方式来组织浏览器端的 Javascript 代码，通过预编译让前端 Javascript 可以直接使用 Node NPM 安装的一些库
###安装

```
npm install transform-loader --save-dev
```
###用法
```
//通过查询参数传递模块
var x = require("!transform-loader?brfs!./file.js");
var x = require("!transform-loader/cacheable?brfs!./file.js"); // 可缓存版本
```

###Options
transforms：一个用于转换配置文件里面test配置项匹配的文件的一个函数数组

```
// entry.js
const thing = require('widget/thing');
```

```
// webpack.config.js
const through = require('through2');

module.exports = {
  module: {
    rules: [
      {
        test: /\.ext$/,
        // NOTE: we've specified an index of 0, which will use the `transform`
        //       function in `transforms` below.
        loader: 'transform-loader?0',
        options: {
          transforms: [
            function transform() {
              return through(
                (buffer) => {
                  const result = buffer
                    .split('')
                    .map((chunk) => String.fromCharCode(127 - chunk.charCodeAt(0)));
                  return this.queue(result).join('');
                },
                () => this.queue(null)
              );
            }
          ]
        }
      }
    ]
  }
}
```

