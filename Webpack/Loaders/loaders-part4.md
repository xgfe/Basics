	by wangpan 2018.11
	
* postcss-loader
* raw-loader
* react-proxy-loader
* restyle-loader
* sass-loader
* script-loader
* source-map-loader
* style-loader

### postcss-loader

install
>npm i -D postcss-loader

postcss.config.js

```
module.exports = {
	plugins: {
		'postcss-cssnext': {}
	}
}
```

webpack.config.js

```
module.exports = {
	module: {
		rules: [
			{
				test: /.\css/
				use: [
					'style-loader',
					{ loader: 'css-loader', options: { importLoaders: 1 } },
					'postcss-loader'
				]
			}
		]
	}
}
```
常用插件列表

1. autoprefixer: 方便写出规范兼容的css，它的来源主要是can i use，插件的配置参数是browsers
	```
	module.exports = {
		plugins: [
			require('autoprefixer')({ browsers: 'last 2 versions' })
		]
	}
	```
2. postcss-partial-import: 让css文件支持@import
3. cssnano : css代码的压缩工具
4. postcss-px2rem: 移动端适配使用的将px转换为rem的插件

推荐阅读：《深入Postcss Web设计》


#### raw-loader

把文件内容作为字符串返回。

install
>npm install -D raw-loader


webpack.config

```
module.exports = {
	module: {
		rules: [
			test: /.\txt$/,
			use: 'raw-loader'
		]
	}
}
```

usage:

```
import txt from 'file.txt'
```


#### restyle-loader

通过hash值来触发样式重载

install
> npm install --save-dev restyle-loader


webpack.config

```
module.exports = {
	{
	  test: /\.css?$/,
	  use: [
	    {
	      loader: "restyle-loader"
	    },
	    {
	      loader: "file-loader",
	      options: {
	        name: "[name].css?[hash:8]"
	      }
	    }
	  ]
	}
}
```

#### sass-loader
对sass/scss css预编译语言进行编译
install
>npm install --save-dev node-sass sass-loader


webpack.config

```
module.exports = {
  module: {
    rules: [{
      test: /\.scss$/,
      use: [{
          loader: "style-loader" // 将 JS 字符串生成为 style 节点
      }, {
          loader: "css-loader" // 将 CSS 转化成 CommonJS 模块
      }, {
          loader: "sass-loader" // 将 Sass 编译成 CSS
      }]
    }]
  }
};
```

Source maps

要启用 CSS source map，需要将 sourceMap 选项作为参数，传递给 sass-loader 和 css-loader

webpack.config

```
module.exports = {
    devtool: "source-map", // any "source-map"-like devtool is possible
    module: {
        rules: [{
            test: /\.scss$/,
            use: [{
                loader: "style-loader"
            }, {
                loader: "css-loader", options: {
                    sourceMap: true
                }
            }, {
                loader: "sass-loader", options: {
                    sourceMap: true
                }
            }]
        }]
    }
};
```


#### script-loader
在全局上下文(global context)执行一次 JS 脚本。在 node.js 中不会运行

install
>npm install --save-dev script-loader

webpack.config.js

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.exec\.js$/,
        use: [ 'script-loader' ]
      }
    ]
  }
}
```

#### demo-source-map
对打包压缩过的文件进行源码映射，前提是该js文件有正确引用

install
>npm i -D source-map-loader


```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: ["source-map-loader"],
        enforce: "pre"
      }
    ]
  }
};
```

#### style-loader

Adds CSS to the DOM by injecting a <style> tag

install
>npm i style-loader --save-dev


webpack.config

```
{
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: "style-loader" },
          { loader: "css-loader" }
        ]
      }
    ]
  }
}
```








