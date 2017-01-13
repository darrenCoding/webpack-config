# webpack配置文件中文详解
## 目的
解决webpack官方文档混乱且无中文翻译的情况
## 说明
此配置文档基于**webpack1.* **，**webpack2略有不同**，请大家务必注意，另此文档只是简单罗列了所有配置选项，详细的使用方法请大家自行学习。
## 常用命令行
```
$ webpack [Options] 

      Options：

         -p                  对打包的代码进行压缩

         --watch             文件发生变化时重新打包

         --config            指定Webpack配置文件

         --progress --colors 终端显示打包过程
```
## 开始
### context
#### 类型：String
```
项目根目录(必须为绝对路径！)

Default : process.cwd()

示例：
module.exports = {
   context : path.resolve(__dirname, "app")
}
```

### entry
#### 类型：String | [String] | Object { <key>: String | [String] }
```
要打包的入口文件，值可以是字符串、数组、对象，指定的路径会相对context选项指定的根目录查找

1、字符串
module.exports = {
   entry : "./entry.js"
}

2、数组
按顺序打包，只生成一个文件
module.exports = {
   entry : ['./entry1','./entry2']
}

3、对象
键名为块名，可以自己指定，键值可以为字符串或数组，块名可在output选项中使用。
module.exports = {
   entry : {
       page1 : './entry',
       page2 : ['.entry1','./entry2']
   },
   output : {
       //打包后输出文件名，name为entry中对应的键名
       filename : '[name].bundle.js'
   }
}
```
### output
#### 类型：Object
```
配置输出信息

如果项目有多个入口，对于每个入口打包后的文件要保证其唯一性，Webpack提供了以下模式来动态生成文件名:
 [id]，异步加载(code splitting)的块的id值
 [name]，入口文件块名
 [hash]，每个入口打包后的哈希值。
 [chunkhash]，在用代码分割时，异步加载的文件的哈希值
 [file]，打包后的文件名(用于生成sourcemap)

```
#### output.path(String)
```
指定输出路径

module.exports = {
   output : {
       path : '__dirname + './build''
   }
}
```
#### output.filename(String)
```
打包后的文件名，filename会相对output.path指定的路径输出，注意不能指定为绝对路径！

module.exports = {
   output : {
       filename : 'bundle.js'
   }
}
```
#### output.publicPath(String)
```
指定打包后的文件的url地址

例如打包后的文件名在线上的访问域名为cdn.leju.com，此时可作如下配置

module.exports = {
   output : {
       path : '__dirname + './build'',
       filename : 'bundle.js',
       publicPath : "http://cdn.leju.com/"
   }
}
打包后的代码在html中的引入路径为http://cdn.leju.com/bundle.js
```
#### output.chunkFilename(String)
```
异步加载(code splitting)的文件打包后的文件名

module.exports = {
   output : {
       path : '__dirname + './build'',
       filename : 'bundle.js',
       chunkFilename : '[id].[chunkhash].js'
   }
}
```
#### output.sourceMapFilename(String)
```
生成的sourcemap的文件名，默认是[file].map
module.exports = {
   output : {
       path : '__dirname + './build'',
       filename : 'bundle.js',
       sourcemap : '[id].[file].map'
   }
}
```
####  output.jsonpFunction(String)
```
此配置用于异步加载公共模块， 较短的函数可以缩小文件的大小，在一个页面里面拥有多个webpack引用的时候，需要使用不同的标识符.

Default : webpackJsonp

补充:
webpack内部实现了一个全局的webpackJsonp函数用于加载处理后的公共文件，
同时webpack会把生成的文件进行编号，每一个文件对应一个id。

注意：用CommonsChunkPlugin插件生成的公共文件必须在其他文件之前引入，
     否者会报webpackJsonp is not defined错误！
```
#### output.pathinfo(Boolean)
```
包含一些模块的评论信息。

Default : false

注意：请勿在生产环境下使用

```
#### output.library(String)
```
如果需将生成的单文件作为一个库供其他开发者在任何模式(AMD，COMMONJS，UMD，GLOBAL)
下使用时可配置library为库名

Default : ''
```
#### output.libraryTarget(String)
```
选择库的导出格式

Default : 'var'

可选值：

"var" - 通过设置一个变量导出: var Library = xxx (default)

"this" - 通过设置 this的属性来导出: this["Library"] = xxx

"commonjs" - 通过设置 exports的属性导出: exports["Library"] = xxx

"commonjs2" - 通过设置 module.exports导出: module.exports = xxx

"amd" - 导出为AMD (视情况可通过output.library来命名)

"umd" - 导出为 AMD, CommonJS2 或者是全局属性

示例：

// 文件名：src/index.js

function Foo() {}

//...

module.exports = Foo;


// 配置如下
{
    output: {
        libraryTarget: "umd",
        library: "Foo"
    }
}
```
#### output.umdNamedDefine(String)
```
如果 output.libraryTarget 被设置为umd且output.library被设置， 
设置该项为true将为AMD module 命名。
```
#### output.sourcePrefix(String)
```
在每一行代码前加上此前缀

Default : '\t'
```
#### output.crossOriginLoading(String)
```
是否允许跨域加载chunks

Default : false

可选值：

false - 禁止.

'anonymous' - 允许，credentials将不随请求被发送.

'use-credentials' - 允许，credentials将随请求被发送.
```
### module
#### 类型：Object
```
配置模块
```
#### module.loaders(Array)
```
配置资源加载器

test: 文件后缀匹配规则
exclude: 不匹配的目录或文件
include: 匹配的目录与文件
loaders: 对应的加载器
loader: 与loaders一样，区别是用 “!” 把loaders拼接在了一起

示例：

module.exports =  {
   module : {
       loaders: [
           {
             test: /\.jsx$/,
             include: [
                 path.resolve(__dirname, "app/src"),
                 path.resolve(__dirname, "app/test")
             ],
             loader: "babel-loader"
           }
       ]
   }
}
```
#### module.preLoaders(Array) module.postLoaders(Array)
```
语法跟module.loaders一样，前置和后置加载loaders.

示例：
module.exports = {
   module : {
        preLoaders: [
           {
              test: /\.js$/,
              loader: 'eslint',
              exclude: /node_modules/
           }
        ],
   }
}

```
#### module.noParse(RegExp|Array|)
```
配置此项可忽略对已知文件的解析

说明：如果一个模块中无其它依赖，可配置此项减少webpack
     打包时间(webpack将不再解析这个文件中的依赖)

示例：

module.exports = {
   module : {
        noParse : [./libs/]
   }
}
```
### resolve
#### 类型：Object
```
解析模块

```
#### resolve.alias(Object)
```
指定某些模块的引用路径

改配置对象键名为模块名，键值为新的路径。

示例：
module.exports = {
   resolve : {
        alias: {
            'vue': 'vue/dist/vue.min.js',
        }
   }
}
```
#### resolve.root(String)
```
模块存放目录(必须为绝对路径)，通常用于添加其他目录到webpack查找路径里.

示例：

module.exports = {
   resolve : {
       root: [
            path.resolve('./app/modules'),
            path.resolve('./vendor/modules')
       ]
   }
}
```
#### resolve.modulesDirectories(Array)
```
设置模块查找路径，查找原理类似于node查找node_modules

Default :  ['web_modules', 'node_modules']

说明：

许多开发者易将此配置与resolve.root配置弄混，区别如下：
resolve.root : 添加到webpack查找范围的绝对路径
resolve.modulesDirectories : 设置webpack查找范围的目录树，参考node查找node_modules原理

如果是想控制webpack查找依赖的深度和查找结构配置此选项，其他情况配置resolve.root
```
#### resolve.fallback(String|Array)
```
如果未在resolve.root或者resolve.modulesDirectories配置的目录下找到相应依赖
可在此选项配置的路径内查找。
```
#### resolve.extensions(Array)
```
配置需解析的模块拓展名数组

Default : ['', '.webpack.js', '.web.js', '.js']

示例：

module.exports = {
    resolve : {
        extensions : ['', '.coffee', '.js', '.css', '.less', '.sass','.jsx']
    }
}
```
### resolveLoader
#### 类型：Object
```
类似resolve但是针对于loaders的配置

Default : 

{
    modulesDirectories: ["web_loaders", "web_modules", "node_loaders", "node_modules"],
    extensions: ["", ".webpack-loader.js", ".web-loader.js", ".loader.js", ".js"],
    packageMains: ["webpackLoader", "webLoader", "loader", "main"]
}
```
### externals
#### 类型：String|Array|RegExp|Function|Object
```
值可以是对象，字符串，函数，正则，数组都会被接受。

此配置比较复杂，详细介绍请查阅官网

示例：

module.exports = {
    externals: {
       'react': 'React' 
    }
}

示例说明：

externals为对象时key是给require时用的，比如require('react')，
value值表示如何在global（即window）中访问到该对象，这里是window.React。

HTML中注意引入顺序即可：
<script src="react.min.js" />
<script src="bundle.js" />

注意：

配置externals后务必手动用script标签引入，否则会报错！

很多开发者有如下疑惑：

这种webpack全局声明模块的方式，跟我直接在自己业务代码里面
使用全局$或者jQuery有什么区别？既然jQuery直接暴露在全局环境了，
还用webpack来声明一次，使用的时候再require引入不是多此一举？

解答：

webpack在打包时找不到$会报错的！因此在配置externals后也必须在html页面手动引入
```

### target(String)
```
编译的目标环境

可选值：

'web' - 浏览器环境(默认)
'webworker' - WebWorker
'node' - node
'async-node’ - node (使用 fs 和 vm 来加载异步chunk)
```

### 常用插件简单介绍
```
代码热替换 - HotModuleReplacementPlugin
生成html文件 - HtmlWebpackPlugin
将css成生文件，而非内联 - ExtractTextPlugin
报错但不退出webpack进程 - NoErrorsPlugin
代码压缩混淆 - UglifyJsPlugin，开发过程中不建议打开
多个 html共用一个js文件(chunk) - CommonsChunkPlugin
清理文件夹 - Clean
调用模块的别名 - ProvidePlugin
替换文件内容 - DefinePlugin

详细使用方法请自行上网查询
```
### 完整版简单示例
```
const webpack = require('webpack');

module.exports = {
    entry: './src/app.js',
    output: {
        path: './bin',
        filename: 'app.bundle.js',
    },
    module: {
        loaders: [{
            test: /\.js?$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
        }]
    },
    plugins: [
        new webpack.optimize.UglifyJsPlugin({
            compress: {
                warnings: false,
            },
            output: {
                comments: false,
            },
        }),
    ]
}
```

此文档只是常用的配置解析，如需完整版请移步至[webpack配置]
(http://webpack.github.io/docs/configuration.html)
