	by liuhongyu 2018.12
* ContextReplacementPlugin
* CopyWebpackPlugin
* DefinePlugin
* DllPlugin
* EnvironmentPlugin
* EvalSourceMapDevToolPlugin


## ContextReplacementPlugin
### 定义

> 重写 `require` 表达式的推断上下文

> ContextReplacementPlugin（上下文替换插件），允许你覆盖查找规则，上下文(Context) 与一个带表达式的 require 语句 相关，例如 require('./locale/' + name + '.json')。遇见此类表达式时，webpack 查找目录 ('./locale/') 下符合正则表达式 (/^.*\.json$/)的文件。由于 name 在编译时(compile time)还是未知的，webpack 会将每个文件都作为模块引入到 bundle 中

### 用法

```js
new webpack.ContextReplacementPlugin(
  resourceRegExp: RegExp,
  newContentResource?: string,
  newContentRecursive?: boolean,
  newContentRegExp?: RegExp
)
```

> 如果资源（或目录）符合 `resourceRegExp` 正则表达式，插件会替换默认资源为 `newContentResource`，布尔值 `newContentRecursive` 表明是否使用递归查找，`newContextRegExp` 用于筛选新上下文里的资源。如果 `newContentResource` 为相对路径，会相对于前一匹配资源路径去解析。

##### 示例一

```js
new webpack.ContextReplacementPlugin(
    /moment[/\\]locale$/,
    /zh-cn|zh-hk|en/
)
```

> 限定查找 `moment/locale` 上下文里符合 `/zh-cn/` 表达式的文件，因此也只会打包这几种本地化内容

##### 示例二

```js
new webpack.ContextReplacementPlugin(
  resourceRegExp: RegExp,
  newContentCallback: (data) => void
);
```

> `newContentCallback` 函数的第一形参为[`上下文模块工厂(ContextModuleFactory)`的 `data` 对象](https://webpack.docschina.org/api/plugins/module-factories/)，你需要覆写该对象的 `request` 属性。
>
> 使用这个回调函数，我们可以动态地将请求重定向到一个新的位置：

```js
new webpack.ContextReplacementPlugin(/^\.\/locale$/, (context) => {
  if ( !/\/moment\//.test(context.context) ) return;

  Object.assign(context, {
    regExp: /^\.\/\w+/,
    request: '../../locale' // 相对路径解析
  });
});
```

## CopyWebpackPlugin

> 将单个文件或整个目录复制到构建目录

### 安装

```
npm i -D copy-webpack-plugin
```

### 用法

```js
new CopyWebpackPlugin([patterns], options)
```

```json
from    定义要拷贝的源目录           from: __dirname + ‘/src/public’
to      定义要拷贝到的目标目录     from: __dirname + ‘/dist’
toType  file 或者 dir         可选，默认是文件
force   强制覆盖先前的插件           可选 默认false
context                         可选 默认base context可用specific context
flatten 只拷贝文件不管文件夹      默认是false
ignore  忽略拷贝指定的文件           可以用模糊匹配
```

##### 示例

```j&#39;sjavascript
const CopyWebpackPlugin = require('copy-webpack-plugin');
const path = require('path');

module.exports = {
    context: path.join(__dirname, 'app'),
    devServer: {
        // This is required for older versions of webpack-dev-server
        // if you use absolute 'to' paths. The path should be an
        // absolute path to your build destination.
        outputPath: path.join(__dirname, 'build')
    },
    plugins: [
        new CopyWebpackPlugin([
            // {output}/file.txt
            { from: 'from/file.txt' },

            // equivalent
            'from/file.txt',

            // {output}/to/file.txt
            { from: 'from/file.txt', to: 'to/file.txt' },

            // {output}/to/directory/file.txt
            { from: 'from/file.txt', to: 'to/directory' },

            // Copy directory contents to {output}/
            { from: 'from/directory' },

            // Copy directory contents to {output}/to/directory/
            { from: 'from/directory', to: 'to/directory' },

            // Copy glob results to /absolute/path/
            { from: 'from/directory/**/*', to: '/absolute/path' },

            // Copy glob results (with dot files) to /absolute/path/
            {
                from: {
                    glob:'from/directory/**/*',
                    dot: true
                },
                to: '/absolute/path'
            },

            // Copy glob results, relative to context
            {
                context: 'from/directory',
                from: '**/*',
                to: '/absolute/path'
            },

            // {output}/file/without/extension
            {
                from: 'path/to/file.txt',
                to: 'file/without/extension',
                toType: 'file'
            },

            // {output}/directory/with/extension.ext/file.txt
            {
                from: 'path/to/file.txt',
                to: 'directory/with/extension.ext',
                toType: 'dir'
            }
        ], {
            ignore: [
                // Doesn't copy any files with a txt extension    
                '*.txt',

                // Doesn't copy any file, even if they start with a dot
                '**/*',

                // Doesn't copy any file, except if they start with a dot
                { glob: '**/*', dot: false }
            ],

            // By default, we only copy modified files during
            // a watch or webpack-dev-server build. Setting this
            // to `true` copies all files.
            copyUnmodified: true
        })
    ]
};
```

## DefinePlugin

### 定义

> 允许在编译时(compile time)配置的全局常量

> `DefinePlugin` 允许创建一个在**编译**时可以配置的全局常量。这可能会对开发模式和发布模式的构建允许不同的行为非常有用。如果在开发构建中，而不在发布构建中执行日志记录，则可以使用全局常量来决定是否记录日志。这就是 `DefinePlugin` 的用处，设置它，就可以忘记开发和发布构建的规则。

```js
new webpack.DefinePlugin({
  // Definitions...
});
```

### 用法

> 每个传进 `DefinePlugin` 的键值都是一个标志符或者多个用 `.` 连接起来的标志符。

- 如果这个值是一个字符串，它会被当作一个代码片段来使用。
- 如果这个值不是字符串，它会被转化为字符串(包括函数)。
- 如果这个值是一个对象，它所有的 key 会被同样的方式定义。
- 如果在一个 key 前面加了 `typeof`,它会被定义为 typeof 调用。

```js
new webpack.DefinePlugin({
  PRODUCTION: JSON.stringify(true),
  VERSION: JSON.stringify('5fa3b9'),
  BROWSER_SUPPORTS_HTML5: true,
  TWO: '1+1',
  'typeof window': JSON.stringify('object'),
  'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV)
});
```

> 注意，因为这个插件直接执行文本替换，给定的值必须包含字符串本身内的**实际引号**。通常，有两种方式来达到这个效果，使用 `'"production"'`, 或者使用 `JSON.stringify('production')`。

# DllPlugin

> 为了极大减少构建时间，进行分离打包

> `DLLPlugin` 和 `DLLReferencePlugin` 用某种方法实现了拆分 bundles，同时还大大提升了构建的速度。
>
> 这个插件是在一个额外的独立的 webpack 设置中创建一个只有 dll 的 bundle(dll-only-bundle)。 这个插件会生成一个名为 `manifest.json` 的文件，这个文件是用来让 [`DLLReferencePlugin`](https://webpack.docschina.org/plugins/dll-plugin#dllreferenceplugin) 映射到相关的依赖上去的。

- `context` (optional): manifest 文件中请求的上下文(context)(默认值为 webpack 的上下文(context))
- `name`: 暴露出的 DLL 的函数名 ([TemplatePaths](https://github.com/webpack/webpack/blob/master/lib/TemplatedPathPlugin.js): `[hash]` & `[name]` )
- `path`: manifest json 文件的**绝对路径** (输出文件)

### 基本用法

```j&#39;s
new webpack.DllPlugin(options);
```

> 在给定的 `path` 路径下创建一个名为 `manifest.json` 的文件。 这个文件包含了从 `require` 和 `import` 的request到模块 id 的映射。 `DLLReferencePlugin` 也会用到这个文件。

#### 示例：

```js
new webpack.DllPlugin({
    path: path.join(__dirname, '.', '[name]-manifest.json'), //生成上文说到清单文件，放在当前build文件下面
    name: '[name]_library'
})

new webpack.DllReferencePlugin({
      context: path.resolve(__dirname, '..'), 
      manifest: require('./vendor-manifest.json')
    })
```

# EnvironmentPlugin

> `EnvironmentPlugin` 是一个通过 [`DefinePlugin`](https://webpack.docschina.org/plugins/define-plugin) 来设置 [`process.env`](https://nodejs.org/api/process.html#process_process_env) 环境变量的快捷方式。

### 用法

```js
new webpack.EnvironmentPlugin(['NODE_ENV', 'DEBUG']);
```

```js
new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
  'process.env.DEBUG': JSON.stringify(process.env.DEBUG)
});
```

> 不同于 [`DefinePlugin`](https://webpack.docschina.org/plugins/define-plugin)，默认值将被 `EnvironmentPlugin` 执行 `JSON.stringify`，从 `process.env` 中取到的值类型均为字符串。

### 带默认值使用

```js
new webpack.EnvironmentPlugin({
  NODE_ENV: 'development', // use 'development' unless process.env.NODE_ENV is defined
  DEBUG: false
});
```

#### 示例：

> 让我们看一下对下面这个用来试验的文件 `entry.js` 执行前面配置的 `EnvironmentPlugin` 的结果：

```javascript
if (process.env.NODE_ENV === 'production') {
  console.log('Welcome to production');
}
if (process.env.DEBUG) {
  console.log('Debugging output');
}
```

> 当在终端执行 `NODE_ENV=production webpack` 来构建时，`entry.js` 变成了这样：

```javascript
if ('production' === 'production') { // <-- NODE_ENV 的 'production' 被带过来了
  console.log('Welcome to production');
}
if (false) { // <-- 使用了默认值
  console.log('Debugging output');
}
```

> 执行 `DEBUG=false webpack` 则会生成：

```
if ('development' === 'production') { // <-- 使用了默认值
  console.log('Welcome to production');
}
if ('false') { // <-- DEBUG 的 'false' 被带过来了
  console.log('Debugging output');
}
```

# EvalSourceMapDevToolPlugin

> 对 eval source map 进行更细粒度的控制
>
> eval 会将每一个module模块，执行eval，执行后不会生成sourcemap文件，仅仅是在每一个模块后，增加sourceURL来关联模块处理前后对应的关系。
>
> 不要同时使用devtool选项和插件，devtool选项在内部添加了插件。

```js
new webpack.EvalSourceMapDevToolPlugin(options)
```

