	by jiangxiang 2019.01

* NpmInstallWebpackPlugin
* PrefetchPlugin
* ProfilingPlugin
* ProgressPlugin
* ProvidePlugin
* SourceMapDevToolPlugin


# NpmInstallWebpackPlugin

通过”自动安装“并”保存依赖“提高开发效率。

通过Ctrl-C去复制一些构建脚本添加那些你也不清楚的依赖是低效的。

取而代之，使用这个插件我们可以像往常一样使用require和import，那些缺少的依赖会自动的被安装。

#### 安装

```sh
$ npm install --save-dev npm-install-webpack-plugin
```

#### 使用

```js
plugins: [
  new NpmInstallPlugin()
]
// 等同于
plugins: [
  new NpmInstallPlugin({
    // 使用 --save 还是 --save-dev
    dev: false,
    // 安装缺省的同伴依赖
    peerDependencies: true,
    // 减少日志的打印
    quiet: false,
    // 在内部使用npm，还不支持yarn
    npm: 'tnpm' -> 'npm'
  });
]
```

你也可以给dev属性传入一个Function，让它动态的进行判断

```js
plugins: [
  new NpmInstallPlugin({
    dev: function(module, path) {
      return [
        "babel-preset-react-hmre",
        "webpack-dev-middleware",
        "webpack-hot-middleware",
      ].indexOf(module) !== -1;
    },
  }),
]
```

# PrefetchPlugin

预取出普通的模块请求(module request)，可以让这些模块在他们被 import 或者是 require 之前就解析并且编译。使用这个预取插件可以提升性能。可以多试试在编译前记录时间(profile)来决定最佳的预取的节点。

### 使用

```js
new webpack.PrefetchPlugin([context], request);
```

### 选项

- ```context``` 文件夹的绝对路径
- ```request``` 普通模块的 request 字符串

http://webpack.github.io/analyse/#hints

# ProfilingPlugin

生成Chrome描述文件，其中包含插件执行的时间。默认情况下输出events.json文件。可以使用outputPath选项提供自定义文件路径。

### 选项

- ```outputPath``` 一个相对路径保存自定义的输出文件

### 使用

```js
new webpack.debug.ProfilingPlugin();
```

自定义输出文件

```js
new webpack.debug.ProfilingPlugin({
  outputPath: 'profiling/profileEvents.json'
});
```

# ProgressPlugin

ProgressPlugin提供了一种在编译期间自定义报告进度的方法。

### 使用

创建ProgressPlugin实例，当报告进度时将调用该钩子函数：

```js
const handler = (percentage, message, ...args) => {
  // e.g. Output each progress message directly to the console:
  console.info(percentage, message, ...args);
};

new webpack.ProgressPlugin(handler);
```

- ```handler``` 钩子函数
- ```percentage``` 一个介于0~1之间的值表示编译执行的进度百分比
- ```message``` 一个当前执行进度的简短描述
- ```...args``` 空或多个描述当前进度的附加字符串

# ProvidePlugin

自动加载模块，而不必到处 import 或 require 。

```js
new webpack.ProvidePlugin({
  identifier: 'module1',
  // ...
});
```
或
```js
new webpack.ProvidePlugin({
  identifier: ['module1', 'property1'],
  // ...
});
```

任何时候，当 identifier 被当作未赋值的变量时，module 就会自动被加载，并且 identifier 会被这个 module 输出的内容所赋值。（模块的 property 用于支持命名导出(named export)）。

> 对于 ES2015 模块的 default export，你必须指定模块的 default 属性。

### 使用：jQuery

要自动加载 jquery，我们可以将两个变量都指向对应的 node 模块：

```js
new webpack.ProvidePlugin({
  $: 'jquery',
  jQuery: 'jquery'
});
```

然后在我们任意源码中：

```js
// in a module
$('#item'); // <= 起作用
jQuery('#item'); // <= 起作用
// $ 自动被设置为 "jquery" 输出的内容
```

### 使用：jQuery 和 Angular 1 

```js
new webpack.ProvidePlugin({
  'window.jQuery': 'jquery'
});
```

### 使用：Lodash Map

```js
new webpack.ProvidePlugin({
  _map: ['lodash', 'map']
});
```

### 使用：Vue.js

```js
new webpack.ProvidePlugin({
  Vue: ['vue/dist/vue.esm.js', 'default']
});
```

# SourceMapDevToolPlugin

本插件实现了对 source map 生成，进行更细粒度的控制。它可以替代 devtool 选项。

```js
new webpack.SourceMapDevToolPlugin(options);
```

### 选项

- ```test(string|regex|array)``` 匹配并生成相关模块的sourcemap（默认支持```.js```，```.mjs```，和```.css```）
- ```include(string|regex|array)``` 匹配的模块为其生成sourcemap
- ```exclude(string|regex|array)``` 匹配的模块不为其生成sourcemap
- ```filename(string)``` 定义输出的sourcemap的文件名（默认是内联的base64字符）
- ```append(string)``` 打印给定的文件源地址，通常是以```#sourceMappingURL```添加注释，```[url]```会被替换成sourcemap文件名，```false```会取消打印文件源地址
- ```moduleFilenameTemplate(string)``` 
- ```module(boolean)``` 表示loader是否生成source map（默认为```true```）
- ```columns(boolean)``` 是否支持列映射（默认为```true```）
- ```noSources(boolean)``` 防止源文件的内容被包含在 source map 里（默认为 ```false```）。
- ```publicPath(string)``` 生成带 public path 前缀的绝对 URL，例如：```https://example.com/project/```。
- ```fileContext(string)``` 使得 [file] 参数作为本目录的相对路径。

> 如果在开发模式下使用自定义配置，需要手动将devtool设置为false，即```devtool: false```

### 使用

### 基本用法

如下的配置等价于```devtool: inline-source-map```

```js
module.exports = {
  // ...
  devtool: false,
  plugins: [
    new webpack.SourceMapDevToolPlugin({})
  ]
};
```

### 排除 vendor 的 map

以下代码会排除 vendor.js 内模块的 source map。

```js
new webpack.SourceMapDevToolPlugin({
  filename: '[name].js.map',
  exclude: ['vendor.js']
});
```

### 在宿主环境外部化 source map

设置 source map 的 URL。在宿主环境需要授权的情况下很有用。

```js
new webpack.SourceMapDevToolPlugin({
  append: '\n//# sourceMappingURL=http://example.com/sourcemap/[url]',
  filename: '[name].map'
});
```

还有一种场景，source map 存储在上层目录中时：

```js
project
|- dist
  |- public
    |- bundle-[hash].js
  |- sourcemaps
    |- bundle-[hash].js.map
```

如下设置：

```js
new webpack.SourceMapDevToolPlugin({
  filename: 'sourcemaps/[file].map',
  publicPath: 'https://example.com/project/',
  fileContext: 'public'
});
```

将会生成以下 URL：

```js
https://example.com/project/sourcemaps/bundle-[hash].js.map
```
