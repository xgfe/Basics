	by zhangjihu 2019.02

* SplitChunksPlugin
* StylelintWebpackPlugin
* TerserWebpackPlugin
* UglifyjsWebpackPlugin
* WatchIgnorePlugin
* Internal webpack plugins


# SplitChunksPlugin

## 介绍

SplitChunksPlugin的前身是CommonsChunkPlugin，Webpack3升级到Webpack4时废弃了Webpack3的CommonsChunkPlugin，引进了SplitChunksPlugin,用optimization.splitChunks 和 optimization.runtimeChunk替代了CommonsChunkPlugin。

首先，CommonsChunkPlugin这个插件是用来提取公共代码的，通过将公共模块提取出来，只在页面加载的时候引入一次，避免依赖重复，提升应用的加载效率，但是有些情况下会导致入口文件过大的问题。

默认情况下SplitChunksPlugin将只会影响按需加载的代码块,因为改变初始化的代码块将会影响html中运行项目需要包含的script标签，增加请求数。

通过optimization.runtimeChunk: true选项，webpack会添加一个只包含运行时(runtime)额外代码块到每一个入口。（译注：这个需要看场景使用，会导致每个入口都加载多一份运行时代码）



## 用法

production模式下，SplitChunksPlugin插件是默认被启用的

首先webpack会根据下述条件自动进行代码块分割：

+ 新代码块可以被共享引用，或者这些模块都是来自node_modules文件夹里面
+ 新代码块大于30kb（min+gziped之前的体积）
+ 按需加载的代码块，并行请求最大数量应该小于或者等于5
+ 初始加载的代码块，并行请求最大数量应该小于或等于3

块打包默认情况下只会影响按需加载模块，因为对初始块也进行优化打包会影响HTML中的script标签数，增加请求数。

默认配置如下：


```
// webpack.config.js

module.exports = {
  //...
  optimization: {
      splitChunks: {
          chunks: "async",  // initial, all模式会将所有来自node_modules的模块分配到一个叫vendors的缓存组；所有重复引用至少两次的代码，会被分配到default的缓存组。默认 Webpack 4 只会对按需加载的代码做分割。如果我们需要配置初始加载的代码也加入到代码分割中，可以设置 splitChunks.chunks 为 'all'。
          minSize: 30000,
          minChunks: 1,
          maxAsyncRequests: 5,
          maxInitialRequests: 3,
          automaticNameDelimiter: '~',
          name: true,
          cacheGroups: {
              vendors: {
                  test: /[\\/]node_modules[\\/]/,
                  priority: -10
              },
              default: {
                  minChunks: 2,
                  priority: -20,
                  reuseExistingChunk: true
              } 
          }
      }
   }
}

```
## 选项

+ chunks: 表示哪些代码需要优化，有三个可选值：initial(初始块)、async(按需加载块)、all(全部块)，默认为async
+ minSize: 表示在压缩前的最小模块大小，默认为30000B（即切割完要生成的新chunk要>30kb，否则不生成新chunk）
+ minChunks: 表示被引用次数，默认为1
+ maxAsyncRequests: 按需加载时候最大的并行请求数，默认为5
+ maxInitialRequests: 一个入口最大的并行请求数(即页面初始化时加载代码块的请求数量)，默认为3
+ automaticNameDelimiter: 命名连接符
+ name: 拆分出来块的名字，默认由块名和hash值自动生成
+ cacheGroups: 缓存组，我们要切割成的每一个新chunk就是一个cache group。默认来说，缓存组会继承splitChunks的配置，但是test、priorty和reuseExistingChunk只能用于配置缓存组。
    * test: 用于控制哪些模块被这个缓存组匹配到
    * priority: 缓存组打包的先后优先级
    * reuseExistingChunk: 如果当前代码块包含的模块已经有了，就不在产生一个新的代码块

在所有代码中，引用模块大小最小为30kb，引用次数最少为1次，按需加载最大请求次数为5，初始化加载最大请求次数为3的所有模块需要拆分到一个单独的代码块中，name表示代码的名字，设置为true则表示根据模块和缓存组秘钥自动生成。

可以通过`optimization.splitChunks.cacheGroups.default: false`禁用`default`缓存组。

`default`缓存组的优先级(priotity)是负数，因此所有自定义缓存组都可以有比它更高优先级（译注：更高优先级的缓存组可以优先打包所选择的模块）（默认自定义缓存组优先级为0）。

缓存组默认将node_modules中的模块拆分带一个叫做vendors的代码块中，将最少重复引用两次的模块放入default中。

一个模块可以被分配到多个缓存组，优化策略会将模块分配至跟高优先级别（priority）的缓存组，或者会分配至可以形成更大体积代码块的组里。

# StylelintWebpackPlugin

## 介绍

StylelintWebpackPlugin是一款强大的，现代的css语法检查和纠错工具，它可以帮助开发者在编写样式文件时避免错误，同时它还可以强迫开发者们形成统一的开发规范，简单来说stylelint就是个css语法检查器。

与stylelint-loader的区别

stylelint-loader只能用于通过require引入的文件或entry文件，但是不允许文件里有@import。

## 安装

```
npm install stylelint --save-dev
npm install stylelint-webpack-plugin --save-dev
```
## 用法

```
// webpack.config.js
const StyleLintPlugin = require('stylelint-webpack-plugin');

module.exports = {
  // ...
  plugins: [
    new styleLintPlugin({
      configFile: '.stylelintrc',
      context: 'src',
      files: '**/*.css',
      fix: true,
      failOnError: false
    })
  ],
  // ...
}

```
## 选项

+ configFile: String，配置文件的路径，可以是一个绝对路径，也可以是一个相对路径
+ context：String，scss文件根目录
+ formatter: Object 指定stylelint运行结果的输出格式
+ failOnError：Boolean 默认false，如果true，在build过程中如果出现错误则会结束build，抛出错误
+ files: 正则匹配想要lint监测的文件，相对路径（option.context）
+ fix：为true时，stylelint会自动修复能修复的错误，不能修复才报错。
+ syntax：指定语法规则，可选scss/less/sugarss，不指定时默认读文件后缀识别。
+ cache：缓存上次检查结果，再次运行只检查脏值，可以提高检查效率。


# UglifyjsWebpackPlugin

## 介绍

UglifyjsWebpackPlugin 用来对js文件进行压缩，从而减小js文件的大小，加速页面加载速度，它是安装webpack时自动安装的插件。

## 安装

webpack =< v3.0.0版本内置UglifyjsWebpackPlugin插件版本为v0.4.6，使用的是uglifyjs v2，直接用即可。
想要使用最新版本v1.0.0（使用的是uglifyjs v3）需要自行安装。

webpack v4.0.0版本将会自动安装UglifyjsWebpackPlugin v1.0.0

```
npm install uglifyjs-webpack-plugin --save-dev
```
## 用法

```
// webpack.config.js

方法一
使用optimization进行配置，optimization的两个属性minimize和minimizer，都是用来处理代码压缩的。
1、minimize 属性值是Boolean类型， 在生产环境下默认是true，即压缩代码，如果为true，默认走的是uglifyjs-webpack-plugin的配置(v4.26以下)
2、minimizer，属性值Array类型，里面可以传入压缩的配置项

module.exports = {
  optimization: {
  	minimize: true,
    /* minimizer: [new UglifyJsPlugin({
    	...
    	})],
    */
  },
};

方法二

const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
  plugins: [
    new UglifyJsPlugin({
        test: /\.js($|\?)/i,
        include: /\/includes/,
        exclude: /\/excludes/,
        cache: flase, // 是否启用文件缓存，用来加快压缩速度。
                     // 启用后js文件名不变并且内容不变就会读取缓存文件，不重新压缩
                     // 缓存目录的默认路径：node_modules/.cache/uglifyjs-webpack-plugin
                     // 可以自己设置缓存路径   cache: 'path/to/cache'
        parallel: true, // 使用多进程并行运行来提高构建速度，并发运行的默认数量：os.cpus().length - 1。也可以自己设置并发运行的数量，强烈建议开启（cps()返回一个数组对象,包括CPU的型号,速度,时间）。
        sourceMap: true,  // 使用资源映射将错误消息位置映射到模块（这会降低编译速度）。
        uglifyOptions: {
          ie8: false,   // 是否支持IE8，默认false
          ecma: 8,   // 支持的ECMAScript版本（5,6,7或8）。影响分析，压缩&&输出选项。默认undefined
          output: {   // 指定额外的输出选项，默认为null
            comments: false,
            beautify: false
          },
          compress: true,  // 设置false来完全跳过压缩。传递一个对象来指定自定义压缩选项，默认为{}
          warnings: false  // 显示压缩时的警告信息，默认false，可以设置"verbose"来显示更详细的信息
        },
        warningsFilter: (src) => true, // 允许过滤uglify警告
        extractComments: true // 是否将评论提取到单独的文件中
    })
  ]
};
```

## 选项

| 属性名称 | 类型 | 默认值 | 描述 |
| ------ | ------ | ------ | ------ |
| test | {RegExp\|Array<RegExp>} | /.js($\|\\?)/i | 测试匹配的文件 |
| include | {RegExp\|Array<RegExp>} | undefined | 包含的文件 |
| exclude | {RegExp\|Array<RegExp>} | undefined | 排除的文件 |
| cache | {Boolean\|String} | false | 启用文件缓存 |
| parallel | {Boolean\|Object} | false | 使用多进程并行运行和文件缓存来提高构建速度 |
| sourceMap | {Boolean} | false | 使用 source map 将错误信息的位置映射到模块（这会减慢编译的速度) |
| uglifyOptions | {Object} | {...defaults} | uglify [选项](https://github.com/mishoo/UglifyJS2/tree/harmony#minify-options) |
| extractComments | {Boolean\|RegExp\|Function<(node, comment) -> {Boolean\|Object}>} | false | 是否将注释提取到单独的文件 |
| warningsFilter | {Function(source) -> {Boolean}} | () => true | 允许过滤 uglify 警告 |

# TerserWebpackPlugin

## 介绍

背景：
2018-11-19webpack升级到4.26，其中的一项更新是代码压缩插件替换，使用terser-webpack-plugin 替换原来的uglifyjs-webpack-plugin。uglifyjs-webpack-plugin这个压缩代码的插件我们很熟悉了，但是uglifyjs-webpack-plugin使用的uglify-es已经不再被维护，取而代之的是一个名为terser的分支。所以webpack官方放弃了使用uglifyjs-webpack-plugin，建议使用 terser-webpack-plugin，虽然做了向后兼容，但是还是可能会出现bug，所以你在升级时留意一下打包压缩是否报错。

TerserWebpackPlugin 用来对js文件进行压缩。

## 安装

```
npm install terser-webpack-plugin --save-dev
```
## 用法

```
// webpack.config.js

const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  optimization: {
  	minimize: true, //默认走的是这个配置 new TerserPlugin({...})
    /*  new TerserPlugin({
        	cache: true,
        	parallel: true
        })
    */
  },
};
```
## 选项
terser-webpack-plugin和uglifyjs-webpack-plugin配置很相似，具体可以参考官网。

| 属性名称 | 类型 | 默认值 | 描述 |
| ------ | ------ | ------ | ------ |
| test | {RegExp\|Array<RegExp>} | /.js($\|\\?)/i | 测试匹配的文件 |
| include | {RegExp\|Array<RegExp>} | undefined | 包含的文件 |
| exclude | {RegExp\|Array<RegExp>} | undefined | 排除的文件 |
| chunkFilter | Function<(chunk) -> boolean> | () => true | 指定哪些文件允许chunk压缩 |
| cache | {Boolean\|String} | false | 启用文件缓存 |
| parallel | {Boolean\|Object} | false | 使用多进程并行运行和文件缓存来提高构建速度 |
| sourceMap | {Boolean} | false | 使用 source map 将错误信息的位置映射到模块（这会减慢编译的速度) |
| terserOptions | {Object} | {...defaults} | uglify [选项](https://github.com/mishoo/UglifyJS2/tree/harmony#minify-options) |
| extractComments | {Boolean\|RegExp\|Function<(node, comment) -> {Boolean\|Object}>} | false | 是否将注释提取到单独的文件 |
| warningsFilter | {Function(source) -> {Boolean}} | () => true | 允许过滤 uglify 警告 |


# WatchIgnorePlugin

## 介绍

无视指定的文件。换句话说，当处于监视模式(watch mode)下，符合给定地址的文件或者满足给定正则表达式的文件的改动不会触发重编译。

## 用法

```
new webpack.WatchIgnorePlugin(paths);
```
## 选项

路径(paths) (array)：一个**正则表达式**或者**绝对路径**的数组。表示符合条件的文件将不会被监视


# Internal webpack plugins

## 介绍

是webpack内部使用的插件列表，如果需要编辑webpack的编译器可以了解下。

