	by wangpeiyu 2018.08

目录：

* plugins
	* plugins
* Server(devServer)
	* devServer
	* devServer.after
	* devServer.allowedHosts
	* devServer.before
	* devServer.bonjour
	* devServer.clientLogLevel
	* devServer.color - CLI only
	* devServer.compress
	* devServer.contentBase
	* devServer.disableHostCheck
	* devServer.filename 
	* devServer.headers 
	* devServer.historyApiFallback
	* devServer.host
	* devServer.hot
	* devServer.hotOnly
	* devServer.https
	* devServer.index
	* devServer.info - CLI only
	* devServer.inline
	* devServer.lazy 
	* devServer.noInfo 
	* devServer.open
	* devServer.openPage
	* devServer.overlay
	* devServer.pfx
	* devServer.pfxPassphrase
	* devServer.port
	* devServer.proxy
	* devServer.progress - 只用于命令行工具(CLI)
	* devServer.public
	* devServer.publicPath 
	* devServer.quiet 
	* devServer.setup
	* devServer.socket
	* devServer.staticOptions
	* devServer.stats 
	* devServer.stdin - CLI only
	* devServer.useLocalIp
	* devServer.watchContentBase
	* devServer.watchOptions 

#  Plugins
配置文件中的 plugins 选项用于以各种方式自定义 webpack 构建过程。webpack 附带了各种内置插件，可以通过 webpack.[plugin-name] 访问这些插件。

### 配置
plugins 配置项接收一个数组，数组里的每一项都是一个要使用的 Plugin 的实例，Plugin 需要的参数通过构造函数传入。

### 使用一个插件
当多个 bundle 共享一些相同的依赖时，可以使用 CommonsChunkPlugin 这个插件来将这些依赖提取到一个共同的bundle中，这样可以避免重复。具体的使用如下：

```
module.exports = {
  //...
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      //...
    })
  ]
};
```

### 使用多个插件
```
var webpack = require('webpack');
// importing plugins that do not come by default in webpack
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var DashboardPlugin = require('webpack-dashboard/plugin');

// adding plugins to your configuration
module.exports = {
  //...
  plugins: [
    // build optimization plugins
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      filename: 'vendor-[hash].min.js',
    }),
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false,
        drop_console: false,
      }
    }),
    new ExtractTextPlugin({
      filename: 'build.min.css',
      allChunks: true,
    }),
    new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/),
    // compile time plugins
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': '"production"',
    }),
    // webpack-dev-server enhancement plugins
    new DashboardPlugin(),
    new webpack.HotModuleReplacementPlugin(),
  ]
};
```

# DevServer
webpack-dev-server 是一个 webpack 可选的本地开发的 server。它通过 nodejs 的 express 来起一个 server 提供静态文件服务，同时它根据配置文件来打包资源，并将资源存在内存中。webpack-dev-server 可以通过配置文件的 devServer 来配置，也可以通过命令行传入参数来配置。通过配置，可以改变 DevServer 的默认行为。
- 常用的配置：contentBase、port、host、historyApiFallback、overlay、stats、quiet、compress、hot和inline
- 只能在命令行中使用的配置：color、info、progress、stdin

### devServer.after
- 类型：function
- 功能：提供在服务器内部的所有其他中间件之后执行自定义中间件的能力。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            after: function(app) {
                // do fancy stuff
            }
        }
    };
    ```

### devServer.allowedHosts
- 类型：array
- 功能：将允许访问开发服务器的服务列入白名单。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            allowedHosts: [
            'host.com',
            'subdomain.host.com',
            'subdomain2.host.com',
            'host2.com'
            ]
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --entry /entry/file --output-path /output/path --allowed-hosts .host.com,host2.com
    ```

### devServer.before
- 类型：function
- 功能：提供在服务器内部的所有其他中间件之前执行自定义中间件的能力，可用于定义自定义处理程序。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            before: function(app) {
                app.get('/some/path', function(req, res) {
                    res.json({ custom: 'response' });
                });
            }
        }
    };
    ```

### devServer.bonjour
- 类型：boolean
- 功能：此选项在启动时通过ZeroConf网络广播服务器
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            bonjour: true
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --bonjour
    ```

### devServer.clientLogLevel
- 类型：string，可能的值：none, error, warning 或者 info（默认值）。
- 功能：阻止内联模式下在开发工具的控制台显示消息
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            clientLogLevel: 'none'
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --client-log-level none
    ```

### devServer.color -CLI only
- 类型：boolean
- 功能：启用/禁用控制台上的颜色。
- 用法：
    ```
    webpack-dev-server --color
    ```

### devServer.compress
- 类型：boolean
- 功能：一切服务都启用 gzip 压缩。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            compress: true
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --compress
    ```

### devServer.contentBase
- 类型：boolean string array
- 功能：告诉服务器从哪里提供内容。只有在你想要提供静态文件时才需要。devServer.publicPath 将用于确定应该从哪里提供 bundle，并且此选项优先。推荐使用绝对路径。
- 用法：
    - 默认情况下，将使用当前工作目录作为提供内容的目录，但是你可以修改为其他目录
        ```
        module.exports = {
            //...
            devServer: {
                contentBase: path.join(__dirname, 'public')
            }
        };
        ```
    - 从多个目录提供内容
        ```
        module.exports = {
            //...
            devServer: {
                contentBase: [path.join(__dirname, 'public'), path.join(__dirname, 'assets')]
            }
        };
        ```
    - 禁用 contentBase
        ```
        module.exports = {
            //...
            devServer: {
                contentBase: false
            }
        };
        ```
- 在命令行中的用法：
    ```
    webpack-dev-server --content-base /path/to/content/dir
    ```

### devServer.disableHostCheck
- 类型：boolean
- 功能：设置为true时，此选项会绕过主机检查。 不建议这样做，因为不检查主机的应用程序容易受到DNS重新绑定攻击。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            disableHostCheck: true
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --disable-host-check
    ```

### devServer.filename
- 类型：string
- 功能：在惰性模式中，此选项可减少编译。 默认在惰性模式，每个请求结果都会产生全新的编译。使用 filename，可以只在某个文件被请求时编译。在不使用惰性加载时没有效果。
- 用法：如果 output.filename 设置为 bundle.js ，filename 使用如下，只有在请求 /bundle.js 时候，才会编译 bundle。
    ```
    module.exports = {
        //...
        devServer: {
            lazy: true,
            filename: 'bundle.js'
        }
    };
    ```

### devServer.headers
- 类型：object
- 功能：在所有响应中添加首部内容。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            headers: {
            'X-Custom-Foo': 'bar'
            }
        }
    };
    ```

### devServer.historyApiFallback
- 类型：boolean object
- 功能：配置 DevServer 请求返回的 HTML 文件
- 用法：
    - 任何请求都返回index.html文件
        ```
        module.exports = {
            //...
            devServer: {
                historyApiFallback: true
            }
        };
        ```
    - 根据不同请求返回不同的 HTML 文件
        ```
        module.exports = {
            //...
            devServer: {
                historyApiFallback: {
                    rewrites: [
                        { from: /^\/$/, to: '/views/landing.html' },
                        { from: /^\/subpage/, to: '/views/subpage.html' },
                        { from: /./, to: '/views/404.html' }
                    ]
                }
            }
        };
        ```
    - 当路径中使用点(dot)（常见于 Angular），你可能需要使用 disableDotRule
        ```
        module.exports = {
            //...
            devServer: {
                historyApiFallback: {
                disableDotRule: true
                }
            }
        };
        ```
- 在命令行中的用法：
    ```
    webpack-dev-server --history-api-fallback
    ```

### devServer.host
- 类型：string
- 功能：指定使用一个 host。默认是 localhost。如果希望服务器外部可访问，指定如下
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            host: '0.0.0.0'
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --host 0.0.0.0
    ```

### devServer.hot
- 类型：boolean
- 功能：配置是否启用模块热替换功能。DevServer 的默认行为是在发现源代码被更新后通过自动刷新整个页面来做到实时预览，开启模块热替换功能后将在不刷新整个页面的情况下通过用新模块替换老模块来做到实时预览。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            hot: true
        }
    };
    ```
- 注意：需要 webpack.HotModuleReplacementPlugin 才能完全启用HMR。 如果使用 --hot 选项启动 webpack 或 webpack-dev-server，则会自动添加此插件，因此您可能不需要将其添加到 webpack.config.js。

### devServer.hotOnly
- 类型：boolean
- 功能：在没有页面刷新的情况下启用热模块替换作为构建失败时的后备。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            hotOnly: true
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --hot-only
    ```

### devServer.https
- 类型：boolean object
- 功能：配置 DevServer 使用 HTTPS 服务，默认情况下，dev-server 通过 HTTP 提供服务。
- 用法：
    - 启用HTTPS服务，使用自签名证书
        ```
        module.exports = {
            //...
            devServer: {
                https: true
            }
        };
        ```
    - 启用HTTPS服务，使用自己的证书，对象会直接传递到 Node.js HTTPS 模块
        ```
        module.exports = {
            //...
            devServer: {
                https: {
                key: fs.readFileSync('/path/to/server.key'),
                cert: fs.readFileSync('/path/to/server.crt'),
                ca: fs.readFileSync('/path/to/ca.pem'),
                }
            }
        };
        ```

### devServer.index
- 类型：string
- 功能：设置索引文件的文件名
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            index: 'index.htm'
        }
    };
    ```

### devServer.info - CLI only
- 类型：boolean
- 功能：输出cli信息，它默认启用。
- 用法：
    ```
    webpack-dev-server --info=false
    ```

### devServer.inline
- 类型：boolean
- 功能：在 dev-server 的内联模式和iframe模式之间切换。默认情况下，应用程序启用内联模式，这意味着一段处理实时重载的脚本被插入到你的 bundle 中，并且构建消息将会出现在浏览器控制台；也可以使用 iframe 模式，它在通知栏下面使用 <iframe> 标签，包含了关于构建的消息。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            inline: false
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --inline=false
    ```
- 注意：推荐使用模块热替换的内联模式，因为它包含来自 websocket 的 HMR 触发器。轮询模式可以作为替代方案，但需要一个额外的入口点：'webpack/hot/poll?1000'。

### devServer.lazy
- 类型：boolean
- 功能：当启用 lazy 时，dev-server 只有在请求时才编译包(bundle)。这意味着 webpack 不会监视任何文件改动。我们称之为“惰性模式”。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            lazy: true
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --lazy
    ```
- 注意：watchOptions 在使用惰性模式时无效；如果使用命令行工具(CLI)，请确保内联模式(inline mode)被禁用。

### devServer.noInfo
- 类型：boolean
- 功能：启用 noInfo 后，诸如「启动时和每次保存之后，那些显示的 webpack 包(bundle)信息」的消息将被隐藏。错误和警告仍然会显示。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            noInfo: true
        }
    };
    ```

### devServer.open
- 类型：boolean
- 功能：控制 DevServer 是否打开浏览器。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            open: true
        }
    };
    ```
- 在命令行中的用法：
    - 打开默认的浏览器
        ```
        webpack-dev-server --open
        ```
    - 打开指定的浏览器
        ```
        webpack-dev-server --open 'Google Chrome'
        ```

### devServer.openPage
- 类型：string
- 功能：指定在打开浏览器时导航到的页面。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            openPage: '/different/page'
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --open-page "/different/page"
    ```

### devServer.overlay
- 类型：boolean object
- 功能：当存在编译器错误或警告时，在浏览器中显示全屏覆盖。 默认情况下禁用。
- 用法：
    - 只显示编译器错误
        ```
        module.exports = {
            //...
            devServer: {
                overlay: true
            }
        };
        ```
    - 显示警告和错误
        ```
        module.exports = {
            //...
            devServer: {
                overlay: {
                warnings: true,
                errors: true
                }
            }
        };
        ```

### devServer.pfx
- 类型：string
- 功能：通过CLI使用时，SSL .pfx文件的路径。 如果在选项中使用，则它应该是.pfx文件的字节流。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            pfx: '/path/to/file.pfx'
        }
    };
    ```
- 在命令行中的用法:
    ```
    webpack-dev-server --pfx /path/to/file.pfx
    ```

### devServer.pfxPassphrase
- 类型：string
- 功能：到SSL PFX文件的密码短语。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            pfxPassphrase: 'passphrase'
        }
    };
    ```
- 在命令行中的用法:
    ```
    webpack-dev-server --pfx-passphrase passphrase
    ```

### devServer.port
- 类型：number
- 功能：指定要监听请求的端口号。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            port: 8080
        }
    };
    ```
- 在命令行中的用法:
    ```
    webpack-dev-server --port 8080
    ```

### devServer.proxy
- 类型：object
- 功能：代理某些URL。当有单独的API后端开发服务器并且希望在同一域上发送API请求时，代理某些URL会很有用。
- 用法：
    - 在 localhost:3000 上有后端服务的话，可以按如下方式启用代理，请求到 /api/users 现在会被代理到请求 http://localhost:3000/api/users
        ```
        module.exports = {
            //...
            devServer: {
                proxy: {
                    '/api': 'http://localhost:3000'
                }
            }
        };
        ```
    - 如果不想始终传递 /api，可以重写路径
        ```
        module.exports = {
            //...
            devServer: {
                proxy: {
                    '/api': {
                        target: 'http://localhost:3000',
                        pathRewrite: {'^/api' : ''}
                    }
                }
            }
        };
        ```
    - 默认情况下，不接受运行在 HTTPS 上，且使用了无效证书的后端服务器。如果你想要接受，修改配置如下
        ```
        module.exports = {
            //...
            devServer: {
                proxy: {
                    '/api': {
                        target: 'https://other-server.example.com',
                        secure: false
                    }
                }
            }
        };
        ```
    - 不想代理所有的请求时，可以基于一个函数的返回值绕过代理，在函数中可以访问请求体、响应体和代理选项。必须返回 false 或路径，来跳过代理请求。例如对于浏览器请求，你想要提供一个 HTML 页面，但是对于 API 请求则保持代理。
        ```
        module.exports = {
            //...
            devServer: {
                proxy: {
                    '/api': {
                        target: 'http://localhost:3000',
                        bypass: function(req, res, proxyOptions) {
                            if (req.headers.accept.indexOf('html') !== -1) {
                                console.log('Skipping proxy for browser request.');
                                return '/index.html';
                            }
                        }
                    }
                }
            }
        };
        ```
    - 如果要代理同一目标的多个特定路径，可以使用具有context属性的一个或多个对象的数组
        ```
        module.exports = {
            //...
            devServer: {
                proxy: [{
                    context: ['/auth', '/api'],
                    target: 'http://localhost:3000',
                }]
            }
        };
        ```
- 注意：默认情况下不会代理对root的请求。 要启用根代理，应将devServer.index选项指定为falsy值
    ```
    module.exports = {
        //...
        devServer: {
            index: '', // specify to enable root proxying
            host: '...',
            contentBase: '...',
            proxy: {
                context: () => true,
                target: 'http://localhost:1234'
            }
        }
    };
    ```

### devServer.progress - CLI only
- 类型：boolean
- 功能：输出运行进度到控制台。
- 用法：
    ```
    webpack-dev-server --progress
    ```

### devServer.public
- 类型：string
- 功能：当使用内联模式(inline mode)并代理 dev-server 时，内联的客户端脚本并不总是知道要连接到什么地方。它会尝试根据 window.location 来猜测服务器的 URL，但是如果失败，你需要这样。
- 用法：
    - dev-server 被代理到 nginx，并且在 myapp.test 上可用
        ```
        module.exports = {
            //...
            devServer: {
                public: 'myapp.test:80'
            }
        };
        ```
- 在命令行中的用法：
    ```
    webpack-dev-server --public myapp.test:80
    ```

### devServer.publicPath
- 类型：string
- 功能：此路径下的打包文件可在浏览器中访问。
- 用法：假设服务器运行在 http://localhost:8080 并且 output.filename 被设置为 bundle.js。默认 publicPath 是 "/"，所以你的包(bundle)可以通过 http://localhost:8080/bundle.js 访问。
    - 修改 publicPath，将 bundle 放在一个目录，注意要确保 publicPath 总是以斜杠(/)开头和结尾。
        ```
        module.exports = {
            //...
            devServer: {
                publicPath: '/assets/'
            }
        };
        ```
    - 使用一个完整的 URL。这是模块热替换所必需的。
        ```
        module.exports = {
            //...
            devServer: {
                publicPath: 'http://localhost:8080/assets/'
            }
        };
        ```
     - 你的包现在可以通过 http://localhost:8080/assets/bundle.js 访问。
### devServer.quiet
- 类型：boolean
- 功能：启用 quiet 后，除了初始启动信息之外的任何内容都不会被打印到控制台。这也意味着来自 webpack 的错误或警告在控制台不可见。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            quiet: true
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --quiet
    ```

### devServer.setup
- 类型：function
- 功能：在这里，可以访问Express应用程序对象并向其添加自己的自定义中间件。 例如，要为某些路径定义自定义处理程序。
- 用法：
    ```
    setup(app){
        app.get('/some/path', function(req, res) {
            res.json({ custom: 'response' });
        });
    }
    ```
- 注意：此选项已弃用，在v3.0.0中删除。

### devServer.socket
- 类型：string
- 功能：要监听的Unix套接字（而不是主机）
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            socket: 'socket'
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --socket socket
    ```

### devServer.staticOptions
- 类型：object
- 功能：可以配置高级选项以从 contentBase 提供静态文件，仅在将contentBase用作字符串时才有效。有关可能的选项，请参阅Express文档。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            staticOptions: {
                redirect: false
            }
        }
    };
    ```

### devServer.stats
- 类型：string object
- 功能：此选项可让您精确控制打包时的信息展示，与quiet或noInfo一起使用时，此选项无效。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            stats: 'errors-only'
        }
    };
    ```

### devServer.stdin - CLI only
- 类型：boolean
- 功能：当stdin结束时，此选项将关闭服务器。
- 用法：
    ```
    webpack-dev-server --stdin
    ```

### devServer.useLocalIp
- 类型：boolean
- 功能：此选项允许浏览器使用您的本地IP打开。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            useLocalIp: true
        }
    };
    ```

### devServer.watchContentBase
- 类型：boolean
- 功能：告诉服务器观察devServer.contentBase选项所服务的文件。 文件更改将触发整页重新加载。默认禁用。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            watchContentBase: true
        }
    };
    ```
- 在命令行中的用法：
    ```
    webpack-dev-server --watch-content-base
    ```

### devServer.watchOptions
- 类型：object
- 功能：与监视文件相关的控制选项。webpack 使用文件系统(file system)获取文件改动的通知。在某些情况下，不会正常工作。例如，当使用 Network File System (NFS) 时。Vagrant 也有很多问题。在这些情况下，需要使用轮询。如果这对文件系统来说太重了的话，你可以修改间隔时间（以毫秒为单位），将其设置为一个整数。
- 用法：
    ```
    module.exports = {
        //...
        devServer: {
            watchOptions: {
                poll: true
            }
        }
    };
    ```
