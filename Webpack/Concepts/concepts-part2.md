	by pengxiaojia 2018.07

### 目录：
* configuration
	* 基本配置
	* 多个Target
	* 使用其他配置语言
* modules
	* webpack模块
	* 支持的模块类型
* module resolution
	* webpack中的解析规则
	* Resolving Loaders
	* 缓存
* dependency graph
	* 如何生成依赖关系
	* Stats Data

## 配置(configuration)

你可能注意到，很少有webpack配置看起来完全相同。这是因为webpack的配置文件，是导出一个对象的JavaScript文件。此对象是由webpack根据对象定义的属性进行解析。

因为webpack配置是标准的Node.js CommonJS模块，你可以做到以下事情：

- 通过 `require(...)` 导入其他文件
- 通过 `require(...)` 使用npm的工具函数
- 使用JavaScript控制流表达式，例如 `?:` 操作符
- 对常用值使用常量或变量
- 编写并执行函数来生成部分配置

请在合适的时机使用这些特性。

虽然技术上可以，但应避免以下做法：

- 在使用webpack命令接口(CLI)时访问CLI参数（应该编写自己的命令接口(CLI)，或者使用 `--env`）
- 导出不确定的值（调用webpack两次应该产生同样的输出文件）
- 编写很长的配置（应该将配置拆分为多个文件）

webpack配置可以有很多种的格式和风格，但为了团队能够易于理解和维护，团队中应始终采取同一种用法、格式和风格。

### 基本配置

webpack.config.js

```
var path = require('path');

module.exports = {
  mode: 'development',
  entry: './foo.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'foo.bundle.js'
  }
};
```

### 多个Target

- 导出为一个函数
- 导出一个Promise
- 导出多个配置对象

**更详细地内容请参考之后配置中的configuration types部分。**

### 使用其他配置语言

webpack接受以多种编程和数据语言编写的配置文件。

- TypeScript
- CoffeeScript
- Babel and JSX

**更详细地内容请参考之后配置中的configuration languages部分。**

## 模块(modules)

在模块化编程中，开发者将程序分解成离散功能块，并称之为模块。

每个模块具有比完整程序更小的接触面，使得校验、调试、测试轻而易举。精心编写的模块提供可靠的抽象和封装界限，使得应用程序中每个模块都具有条理清楚的设计和明确的目的。

Node.js从最一开始就支持模块化编程。然而，在web，模块化的支持正缓慢到来。在web存在多种支持JavaScript模块化的工具，这些工具各有优势和限制。webpack基于从这些系统获得的经验教训，并将模块的概念应用于项目中的任何文件。

### 什么是webpack模块

与Node.js模块相比较，webpack模块能够以各种方式表达它们的依赖关系，例子如下：

- ES2015 `import` 语句
- CommonJS require() 语句
- AMD define 和 require 语句
- css/sass/less 文件中的 `@import` 语句
- 样式( `url(...)` )或HTML文件（ `<img src=...>` ）中的图片链接(image url)

*webpack1 需要特定的loader来转换ES2015 `import` ，然而通过webpack2可以直接使用。*

### 支持的模块类型
webpack通过loder可以支持各种语言和预处理器编写模块。loader描述了webpack如何处理非JavaScript(non-JavaScript)模块，并且在bundle中引入这些依赖。webpack社区已经为各种流行语言和语言处理器构建了loader，包括：

- CoffeeScript
- TypeScript
- ESNext(Babel)
- Sass
- Less
- Stylus

总的来说，webpack提供了可定制的、强大和丰富的API，允许任何技术栈使用webpack，保持了在开发、测试和生成流程中**无侵入性(non-opinionated)**。

**有关完整的列表，请参看之后的loader章节或自己编写。**

## 模块解析(module resolution)

resolver是一个库(library),用于帮助找到模块的绝对路径。一个模块可以作为另一个模块的依赖模块，然后被后者引用。如下：

```
import foo from 'path/to/module'
// 或者
require('path/to/module')
```
所依赖的模块可以是来自应用程序代码或第三方的库(library)。resolver帮助webpack找到bundle中需要引入的模块代码，这些代码在包含每个 `require` / `import`语句中。当打包模块时，`webpack` 使用enhanced-resolve来解析文件路径

### webpack中的解析规则

使用 `enhanced-resolve` ,webpack能够解析三种文件路径：

- 绝对路径
- 相对路径
- 模块路径

### 绝对路径

```
import "/home/me/file";

import "C:\\Users\\me\\file";
```

已经取得文件的绝对路径，因此不需要进一步再做解析。

### 相对路径

```
import "../src/file1";
import "./file2";
```

在这种情况下，使用 `import` 或 `require` 的资源文件(resource file)所在的目录被认为是上下文目录(context directory)。在 `import/require` 中给定的相对路径，会添加此上下文路径(context path)，以产生模块的绝对路径(absolute path)。

### 模块路径

```
import "module";
import "module/lib/file";
```

模块将在 `resolve.modules` 中指定的所有目录内搜索。也可以替换初始模块路径，此替换路径通过使用 `resolve.alias` 配置选项来创建一个别名。

一旦根据上述规则解析路径后，解析器(resolver)将检查路径是否指向文件或目录。如果路径指向一个文件：

- 如果路径具有文件扩展名，则直接将文件打包。
	 否则，将使用[ `resolve.extensions` ]选项作为文件扩展名来解析，此选项告诉解析器在解析中能够接受哪些扩展名（例如 `.js` ， `.jsx	`） 。

如果路径指向一个文件夹，则采取以下步骤找到具有正确扩展名的正确文件：

- 如果文件夹中包含 `package.json` 文件，则按照顺序查找 `resolve.mainFields` 配置选项中指定的字段，并且 `package.json` 中的第一个这样的字段确定文件路径。
- 如果 `package.json` 文件不存在或者 `package.json` 文件中的main字段没有返回一个有效路径，则按照顺序查找 `resolve.mainFiles` 配置选项中指定的文件名，看是否能在 import/require 目录下匹配到一个存在的文件名。
- 文件扩展名通过 `resolve.extensions` 选项采用类似的方法进行解析。

webpack根据构建目标为这些选项提供了合理的默认配置。

### 解析Loader(Resolving Loaders)

Loader解析遵循与文件解析器指定的规则相同的队则。但是 `resolveLoader` 配置选项可以用来为Loader提供独立的解析规则。

### 缓存

每个文件系统访问都被缓存，以便更快触发对同一文件的多个并行或串行请求。在观察模式下，只有修改过的文件会从缓存中摘出。如果关闭观察模式，在每次编译前清理缓存。

**更多详细地模块解析内容，请参考之后配置章节的解析(resolve)部分。**

## 依赖图(dependency graph)

任何时候，一个文件依赖于另一个文件，webpack就把此视为文件之间有依赖关系。这使得webpack可以接收非代码资源（例如图像或web字体），并且可以把它们作为依赖提供给应用程序。

webpack从命令行或配置文件中定义的一个模块列表开始，处理应用程序。从这个入口起点开始，webpack递归地构建一个依赖图，这个依赖图包含着应用程序所需的每个模块，然后将所有这些模块打包为少量的bundle - 通常只有一个 - 可由浏览器加载

*对于HTTP/1.1客户端，由webpack打包应用程序会尤其强大，因为在浏览器发起一个新请求时，它能够减少应用程序必须等待的实践。对于HTTP/2，可以使用代码拆分以及通过webpack打包来实现最佳优化*


**如何生成依赖关系**

通过 webpack 编译源文件时，用户可以生成包含有关于模块的统计数据的 JSON 文件。这些统计数据不仅可以帮助开发者来分析应用的依赖图表，还可以优化编译的速度。这个 JSON 文件可以通过以下的命令来生成:

```
webpack --profile --json > compilation-stats.json
```

这个标识是告诉 webpack compilation-stats.json 要包含依赖的图表以及各种其他的编译信息。一般来说，也会把 --profile 一起加入，这样每一个包含自身编译数据的模块对象(modules object) 都会添加 profile 。

为了能直观地观察依赖关系，可以访问[可视化依赖关系分析](http://webpack.github.io/analyse/)

**详细地Stats Data内容，可以参看API模块的Stats Data部分**concepts-part1