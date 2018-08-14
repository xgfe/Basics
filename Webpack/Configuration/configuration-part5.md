	by anjing 2018.08
	
* 构建目标(targets)
	* target
	* string
	* function
* watch 和 watchOptions
	* watch
	* watchOptions
	* watchOptions.aggregateTimeout
	* watchOptions.ignored
	* watchOptions.poll
	* info-verbosity
	* 故障排除
	* 发现修改，但并未做处理
	* 没有足够的文件观察者
	* MacOS fsevents Bug
	* Windows Paths
	* Vim
	* 在 WebStorm 中保存
* 外部扩展(externals)
	* externals
	* string
	* array
	* object
	* function
	* regex

# Target

webpack 能够为多种环境或 *target* 构建编译。想要理解什么是 `target` 的详细信息，请阅读 [target 概念页面](https://webpack.docschina.org/concepts/targets)。

#### target

`string | function(compiler)`

告知 webpack 为目标(target)指定一个环境。

#### string

通过 [`WebpackOptionsApply`](https://github.com/webpack/webpack/blob/master/lib/WebpackOptionsApply.js) ，可以支持以下字符串值：

| 选项                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| async-node          | 编译为类 Node.js 环境可用（使用 fs 和 vm 异步加载分块）      |
| atom                | `electron-main` 的别名                                       |
| electron            | `electron-main` 的别名                                       |
| electron-main       | 编译为 [Electron](https://electronjs.org/) 主进程。          |
| `electron-renderer` | 编译为 [Electron](https://electronjs.org/) 渲染进程，使用 `JsonpTemplatePlugin`, `FunctionModulePlugin` 来为浏览器环境提供目标，使用 `NodeTargetPlugin` 和 `ExternalsPlugin` 为 CommonJS 和 Electron 内置模块提供目标。 |
| node                | 编译为类 Node.js 环境可用（使用 Node.js `require` 加载 chunk） |
| node-webkit         | 编译为 Webkit 可用，并且使用 jsonp 去加载分块。支持 Node.js 内置模块和 [`nw.gui`](http://docs.nwjs.io/en/latest/) 导入（实验性质） |
| web                 | 编译为类浏览器环境里可用**（默认）**                         |
| webworker           | 编译成一个 WebWorker                                         |

例如，当 *target* 设置为 `"electron"`，*webpack* 引入多个 electron 特定的变量，也就能正确的对Electron环境下的代码进行编译了。

#### function

如果传入一个函数，此函数调用时会传入一个 compiler 作为参数。如果以上列表中没有符合你的要求的预定义的目标(target)，请将其设置为一个函数。

例如，如果你不需要使用以上任何插件：

```
const options = {
	target: () => undefined
};
```

或者可以使用你想要指定的插件:

```
const webpack = require("webpack");

const options = {
  target: (compiler) => {
    compiler.apply(
      new webpack.JsonpTemplatePlugin(options.output),
      new webpack.LoaderTargetPlugin("web")
    );
  }
};
```

JsonpTemplatePlugin：

块被封装到JSONP调用。入口块中包含一个加载算法。它通过添加<Script >标签来加载块。

`options`是输出选项。

`options.jsonpFunction` 是JSONP函数。

`options.publicPath`用作加载的路径。

`options.chunkFilename`是文件块下的文件名。

# watch和watchOpitons

webpack 可以监听文件变化，当它们修改后会重新编译。这个页面介绍了如何启用这个功能，以及当 watch 无法正常运行的时候你可以做的一些调整。

####  watch

`boolean`

启用 Watch 模式。这意味着在初始构建之后，webpack 将继续监听任何已解析文件的更改。Watch 模式默认关闭。

```
watch: false
```

webpack-dev-server 和 webpack-dev-middleware 里 Watch 模式默认开启。

#### watchOptions

`object`

一组用来定制 Watch 模式的选项：

```
watchOptions: {
  aggregateTimeout: 300,//防止重复按键，300毫秒内算按键一次
  poll: 1000 //监测修改的时间(ms)
}
```

#### watchOptions.aggregateTimeout

`number`

当第一个文件更改，会在重新构建前增加延迟。这个选项允许 webpack 将这段时间内进行的任何其他更改都聚合到一次重新构建里。以毫秒为单位：

```
aggregateTimeout: 300 // 默认值
```

#### watchOptions.ignored

对于某些系统，监听大量文件系统会导致大量的 CPU 或内存占用。这个选项可以排除一些巨大的文件夹，例如 `node_modules`：

```
ignored: /node_modules/ //不监测
```

也可以使用 [anymatch](https://github.com/micromatch/anymatch) 模式：

```
ignored: "files/**/*.js"
```

#### watchOptions.poll

`boolean` `number`

通过传递 `true` 开启 [polling](https://whatis.techtarget.com/definition/polling)，或者指定毫秒为单位进行轮询。

```
poll: 1000 // 每秒检查一次变动
```

如果监听没生效，试试这个选项吧。Watch 在 NFS 和 VirtualBox 机器上不适用。

#### Info-verbosity

`string`: `none` `info` `verbose`

控制生命周期消息的详细程度，例如 `Started watching files(开始监听文件)...` 日志。将 `info-verbosity` 设置为 `verbose`，还会额外在增量构建的开始和结束时，向控制台发送消息。`info-verbosity`默认设置为 `info`。

```
webpack --watch --info-verbosity verbose
```

#### 故障排除

如果您遇到任何问题，请查看以下注意事项。对于 webpack 为何会忽略文件修改，这里有多种原因。

#### 发现修改，但并未做处理

在运行 webpack 时，通过使用 --progress 标志，来验证文件修改后，是否没有通知 webpack。如果进度显示保存，但没有输出文件，则可能是配置问题，而不是文件监视问题。

```
webpack --watch --progress
```

#### 没有足够的文件观察者

确认系统中有足够多的文件观察者。如果这个值太低，webpack 中的文件观察者将无法识别修改：

```
cat /proc/sys/fs/inotify/max_user_watches
```

Arch 用户，请将 `fs.inotify.max_user_watches=524288` 添加到 `/etc/sysctl.d/99-sysctl.conf` 中，然后执行 `sysctl --system`。 Ubuntu 用户（可能还有其他用户）请执行：`echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p`。

#### MacOS fsevents Bug

在 MacOS 中，某些情况下文件夹可能会损坏。请参阅[这篇文章](https://github.com/livereload/livereload-site/blob/master/livereload.com/_articles/troubleshooting/os-x-fsevents-bug-may-prevent-monitoring-of-certain-folders.md)。

#### Windows Paths

因为 webpack 期望获得多个配置选项的绝对路径（如 `__dirname + "/app/folder"`），所以 Windows 的路径分隔符 `\` 可能会破坏某些功能。

使用正确的分隔符。即 `path.resolve(__dirname, "app/folder")` 或 `path.join(__dirname, "app", "folder")`。

#### Vim

在某些机器上，Vim 预先将 [backupcopy 选项](http://vimdoc.sourceforge.net/htmldoc/options.html#'backupcopy') 设置为 `auto`。这可能会导致系统的文件监视机制出现问题。将此选项设置为 `yes` 可以确保创建文件的副本，并在保存时覆盖原始文件。

`:set backupcopy=yes`

####  在 WebStorm 中保存

使用 JetBrains WebStorm IDE 时，你可能会发现保存修改过的文件，并不会按照预期触发观察者。尝试在设置中禁用`安全写入(safe write)`选项，该选项确定在原文件被覆盖之前，文件是否先保存到临时位置：取消选中 `File > Settings... > System Settings > Use "safe write" (save changes to a temporary file first)`。

# externals

`externals` 配置选项提供了「从输出的 bundle 中排除依赖」的方法。相反，所创建的 bundle 依赖于那些存在于用户环境(consumer's environment)中的依赖。此功能通常对 **library 开发人员**来说是最有用的，然而也会有各种各样的应用程序用到它。

> **用户(consumer)**，在这里是指，引用了「使用 webpack 打包的 library」的所有终端用户的应用程序(end user application)。

#### externals

`string` `array` `object` `function` `regex`

不是将某些 `import` 的包(package)**打包**到 bundle 中，而是在运行时(runtime)从外部获取这些*扩展依赖(external dependencies)*。

例如，从 CDN 引入 [jQuery](https://jquery.com/)，而不是把它打包：

**index.html**

```
<script
  src="https://code.jquery.com/jquery-3.1.0.js"
  integrity="sha256-slogkvB1K3VOkzAI8QITxV3VzpOnkeNVsKvtkYLMjfk="
  crossorigin="anonymous">
</script>
```

**webpack.config.js**

```
externals: {
  jquery: 'jQuery'
}
```

这样就剥离了那些不需要改动的依赖模块，换句话，下面展示的代码还可以正常运行：

```
import $ from 'jquery';

$('.my-element').animate(...);

```

具有外部依赖(external dependency)的 bundle 可以在各种模块上下文(module context)中使用，例如 [CommonJS, AMD, 全局变量和 ES2015 模块](https://webpack.docschina.org/concepts/modules)。外部 library 可能是以下任何一种形式：

- **root**：可以通过一个全局变量访问 library（例如，通过 script 标签）。
- **commonjs**：可以将 library 作为一个 CommonJS 模块访问。
- **commonjs2**：和上面的类似，但导出的是 `module.exports.default`.
- **amd**：类似于 `commonjs`，但使用 AMD 模块系统。

可以接受各种语法……

引入一个`demo`方法:

1. 传统方式：`script`标签

```
<script src="demo.js"></script>
<script>demo();</script>
```

2. AMD

```
define(['demo'], function(demo) {
demo();
});
```

3. commonjs 方式

```
const demo = require('demo');

demo();
```

4. ES6 module

```
import demo from 'demo';

demo();
```

#### string

请查看上面的例子。属性名称是 `jquery`，表示应该排除 `import $ from 'jquery'` 中的 `jquery` 模块。为了替换这个模块，`jQuery` 的值将被用来检索一个全局的 `jQuery` 变量。换句话说，当设置为一个字符串时，它将被视为`全局的`（定义在上面和下面）。

#### array

```
externals: {
  subtract: ['./math', 'subtract']
}
```

`subtract: ['./math', 'subtract']` 转换为父子结构，其中 `./math` 是父模块，而 bundle 只引用 `subtract` 变量下的子集。

#### object

```
externals : {
  react: 'react'
}

// 或者

externals : {
  lodash : {
    commonjs: "lodash",
    amd: "lodash",
    root: "_" // 指向全局变量
  }
}

// 或者

externals : {
  subtract : {
    root: ["math", "subtract"]
  }
}
```

此语法用于描述外部 library 所有可用的访问方式。这里 `lodash` 这个外部 library 可以在 AMD 和 CommonJS 模块系统中通过 `lodash` 访问，但在全局变量形式下用 `_` 访问。`subtract` 可以通过全局 `math` 对象下的属性 `subtract` 访问（例如 `window['math']['subtract']`）。

library和libraryTarget使用场景组件开发，而externals使用场景是外部依赖不需要打包进bundle。

libraryTarget的配置，我们使用umd方式，这样便可以用任何一种引入方式，即支持cmd，amd，及全局。

#### function

定义你自己的函数来控制从webpack中外化的行为可能是有用的。例如，[webpack-node-externals会](https://www.npmjs.com/package/webpack-node-externals)从node_modules中排除所有模块，并向白名单包提供一些选项。

它基本上归结为以下：

```
externals: [
  function(context, request, callback) {
    if (/^yourregex$/.test(request)){
      return callback(null, 'commonjs ' + request);
    }
    callback();
  }
],
```

 `'commonjs ' + request` 定义需要被外部模块的类型。

#### regex

匹配给定正则表达式的每个依赖项都将从输出包中排除。

```
externals: /^(jquery|\$)$/i
```

在这种情况下，任何名为jQuery的依赖项，大写或不是或$都将被外化。

关于如何使用此 externals 配置的更多信息，请参考[如何编写 library](https://webpack.docschina.org/guides/author-libraries)。