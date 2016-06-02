title: webpack在PC项目中的应用
date: 2016-02-29 11:52:00
tags: webpack
---

好东西，总是要使用的。

## webpack是什么工具

*webpack is a module bundler*

正如官网对webpack的描述，它是一种模块化加载器，当然也不仅仅限于此。某种程度上来说，可以代替某些`gulp`的功能，至少有些还是无法替代的。在webpack中所有的资源都会被视作模块来处理，为了应对这样的情况，webpack有对应的`loader`机制来处理，另外shim，plugins，和其他构建工具，一样一样的，更多的细节，需要你在实际的应用中慢慢去体会了。

## webpack的使用方法

安装：npm install webpack --verbose --save-dev

webpack认为一个项目（或者一个页面），总有一个入口文件，就像C语言中总有一个main函数一样。假设，我们创建两个文件`./mian.js`和`./query.js`，并且将`main.js`做为我们项目的入口文件。

`query.js`

```JavaScript
    module.exports = function(){
         var version = 1.0.0;
         console.log(version)
    }
```

`main.js`

```JavaScript
    var query = require('./query');
    query();
```

创建一个webpack.config.js文件

```JavaScript
    var config = {
        entry:'./main.js',
        ouptut:{
            path:'./js'
            filename:'main.js'
        }
    }
    module.exports = config;
```

在你的终端上运行`webpack`即可。

## webpack配置详解

`entry`：

entry属性做为可配置的入口，比如上面所写的`./main.js`。entry有三种写法，每一个入口可以称之为一个chunk。

- 如果为字符串，只会打包一个`顺序依赖`的模块，输出则根据output配置而定。
- 如果为数组，只会打包一个`顺序依赖`的模块，合并到最后一个模块时导出，输出则根据output配置而定。
- 如果为对象，则会根据入口打包多个`顺序依赖`的模块，key名会根据在output的配置输出。

`output`：

输出规则，在此对象中设置。

- path 设置输出的文件路径
- filename 设置输出文件名，filename可以有多种配置，比如`main.js`，`[id].js`，`[name].js`，`[hash].js`等
- publicPath 设置资源的访问路径
- library 设置模块导出的类名
- libraryTarget:'umd' 设置模块兼容模式
- umdNamedDefine:true  同上

`devtool`：

将devtool设置为`source-map`，在开发调试阶段非常有用，它的模式非常多，我有搞的比较晕。


`loader`：

loader机制应该是webpack中非常重要的部分了，它是一系列资源的最终执行者。一般情况下，你可以访问：[webpack loader](http://webpack.github.io/docs/list-of-loaders.html)来访问可用loader列表。

比如现在我想将.html类型的文件，当做一个模块来载入。

    npm install raw-loader


```JavaScript
    module:{
        loaders:[
            {
                test:/\.html$/,
                loader:'raw',
                exclude:/(node_modules)/
            }
        ]
    }
```

每一个loader都可以用一个对象来描述，test是你的匹配规则，loader是你要载入的loader，exclude是你在执行规则是想忽略的目录。

`plugins`：

webpack的插件机制也非常的重要，其内置了多种插件，比如混淆，压缩等等。插件列表可以访问：[list of plugins](http://webpack.github.io/docs/list-of-plugins.html)。

正常情况下可以使用官方自带的插件：

```JavaScript
    new webpack.optimize.UglifyJsPlugin({
        compress: {
            warnings: false
        }
    })
```

当然，我们也可以引入第三方插件，使用你的npm install吧。

`resolve`：

此配置可以对一些常用模块设置别名，比如`a.js`放置在`./src/module/address/`中，每次载入模块需要var a = require('./src/module/address/a');名字非长，如果设置别名了，只需要var a = require('a')；

```JavaScript
    resolve:{
        alias:{
            "RequestModel":path.resolve(__dirname,'src/lib/request.model')
        }
    },
```

还可以设置访问路径，以及模块载入后缀。

```JavaScript
    resolve:{
        root:path.resolve(filePath,'/src'),
        extensions:['','.js']
    }
```

`externals`：

此项配置可以将某些库设置为外部引用，内部不会打包合并进去。

```JavaScript
    externals:{
        jquery:'window.jQuery'
    }
```

## 在我们PC项目中的应用

我们公司内部的项目，也开始应用npm scripts来做执行钩子，webpack来做构建，首先设计三个命令：

- npm run start
- npm run dev
- npm run build

**配置npm run start**

本地服务器的启动，我们没有使用webpack官方提供的webpack-dev-server，而是采用了 browser-sync。

```JavaScript
var browser = require('browser-sync');
var browserSync = browser.create();
var PORT = 4000
var loadMap = [
    'modules/*.*',
    'src/**/*.*',
    './*.html',
       './web/*.html'
];
gulp.task('server',[], function() {
    // content
        browserSync.init({
            server:'./',
            port:PORT
        });
        gulp.watch(loadMap, function(file){
            console.log(file.path)
            browserSync.reload()
        });
});
```

利用gulp写了一个脚本任务，在package.json文件中设置：

```JavaScript
    scripts:{
        "start":"gulp server"
    }
```

**了解我们项目的实际需求**

我们的项目是一个多页面项目，并不像单页应用一样（业务编程可以打包成一个），首先我们需要设计一个良好的目录结构，如下：

- web 目录放置*.html页面
- style 目录放置*.css文件，另外在此目录中放置了less源文件
- src 目录放置了我们的所有*.js文件
- mock  内置的模拟数据，放置在此
- img 图片放置目录
- link npm下载不了的第三方库放置在此
- YYT_PC_Modules 内部编写的模块，放置在此
- YYT_PC_Component 内部编写的组件，放置在此

编写一个map.json文件，用来维护多入口的关系。当然我们的webpack.dev.config.js文件，放置在根目录。最后的build阶段应该输出一个新的目录`dist`这个目录中放置的应该是所有build完成的资源，包括*.html文件。

它应该才是我们最终的发布目录。

**配置npm run dev**

对于CSS我们的期望是一个新的link而不是style内嵌，所以还需要做一些额外的事情，先下载loader和插件。

列表：

    "less-loader": "^2.2.2",
    "raw-loader": "^0.5.1",
    "style-loader": "^0.13.0",
    "css-loader": "^0.23.1",
    "eslint": "^2.2.0",
    "eslint-loader": "^1.3.0",
    "extract-text-webpack-plugin": "^1.0.1",

`raw-loader`主要用来解决模板载入的问题，模块当做一个变量直接载入到业务编程中。

配置我们的CSS：

```JavaScript
   // webpack.dev.config.js
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var extractLESS = new ExtractTextPlugin('../style/[name].css');
    {
        test: /\.less$/i,
        loader: extractLESS.extract(['css','less'])
    }

    //入口js文件

    require('../style/less/index.less')
```

`ExtractTextPlugin`的作用就是将CSS单独输出一个文件，这个文件名依赖于entry写的入口文件名。

配置我们的多页面JS：

```JavaScript
    //利用了entry的对象写法
    {
        "index.main":"./src/index.mian.js"
    }

    //然后在输出是用[name]代替之前的'index.main.js'
```

提取JS文件中的公共部分：

webpack自带的一个插件，可以提取合并打包时的公共部分，只要在页面载入时，放置在合并打包后文件的前面。

```JavaScript
new optimize.CommonsChunkPlugin('common.js')
```

**完整的dev构建脚本**

```JavaScript
var webpack = require('webpack');
var path = require('path');
var fs = require('fs');
var plugins = [];
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var optimize = webpack.optimize
var extractLESS = new ExtractTextPlugin('../style/css/[name].css');
plugins.push(extractLESS);
plugins.push(new optimize.CommonsChunkPlugin('common.js'));
var sourceMap = require('./map.json').source;
var YYT_PC_Modules = 'link/YYT_PC_Modules/';
var YYT_PC_Component = 'link/YYT_PC_Component/';
var config = {
    entry: sourceMap,
    output: {
        path: path.resolve(__dirname + '/js'),
        filename: '[name].js'
    },
    devtool: 'source-map',
    module: {
        loaders: [
            {
                test: /\.html$/,
                loader: 'raw',
                exclude: /(node_modules)/
            },
            {
                test: /\.js$/,
                loader: 'eslint-loader',
                exclude: /(node_modules)/
            },
            {
                test: /\.less$/i,
                loader: extractLESS.extract(['css', 'less'])
            },
            {
                test: /\.(png|jpg)$/,
                loader: 'url-loader?limit=8192'
            }
        ]
    },
    plugins: plugins,
    resolve: {
        alias: {
            "tplEng": path.resolve(__dirname, 'link/template'),  //模板引擎
            "BaseModel": path.resolve(__dirname, YYT_PC_Modules + 'baseModel'),
            "BaseView": path.resolve(__dirname, YYT_PC_Modules + 'baseView'),
            "store": path.resolve(__dirname, YYT_PC_Modules + 'store/locationStore'),
            "cookie": path.resolve(__dirname, YYT_PC_Modules + 'store/cookie'),
            "url": path.resolve(__dirname, YYT_PC_Modules + 'util/url'),
            "tools": path.resolve(__dirname, YYT_PC_Modules + 'util/tools'),
            "FlashAPI": path.resolve(__dirname,YYT_PC_Modules + 'util/FlashAPI'),
            "DateTime": path.resolve(__dirname, YYT_PC_Modules + 'util/DateTime'),
            "pwdencrypt": path.resolve(__dirname, YYT_PC_Modules + 'crypto/pwdencrypt'),
            "secret": path.resolve(__dirname, YYT_PC_Modules + 'crypto/secret'),
            "UploadFile": path.resolve(__dirname, YYT_PC_Component + 'feature/UploadFile'),
            "AjaxForm": path.resolve(__dirname, YYT_PC_Component + 'feature/AjaxForm'),
            "Scrollbar": path.resolve(__dirname, YYT_PC_Component + 'feature/Scrollbar'),
            "LoginBox": path.resolve(__dirname, YYT_PC_Component + 'business/LoginBox/'),
            "UserModel": path.resolve(__dirname, YYT_PC_Component + 'business/UserModel/'),
            "UploadFileDialog": path.resolve(__dirname, YYT_PC_Component + 'business/UploadFileDialog/'),
            "ui.Dialog": path.resolve(__dirname, YYT_PC_Component + 'ui/dialog/'),
            "ui.Confirm": path.resolve(__dirname, YYT_PC_Component + 'ui/confirm/'),
            "ui.MsgBox": path.resolve(__dirname, YYT_PC_Component + 'ui/msgBox/'),
            "config": path.resolve(__dirname, 'src/lib/config')
        }
    },
    externals: {
        jquery: 'window.jQuery',
        backbone: 'window.Backbone',
        underscore: 'window._'
    }
};
// console.log(path.resolve(__dirname,'node_modules/jquery/dist/jquery.js'))
module.exports = config;

```


**问题**

问题一：在windows机器上如果你要设置环境变量（也许是我没有找到问题的所在，但是提出来，主要是Mac用习惯了。）

```JavaScript
    scripts:{
        "dev":"WEB_PACK=1 webpack --watch --config webpack.dev.config.js "
    }
```

在webpack.dev.config.js文件中不能正确的获取环境变量，所以重新写了一个build文件，webpack.build.config.js来做最后的构建。

问题二：构建之后的文件hash化后如何更新HTML中的路径

这个问题，最后没有采用webpack来做，而是使用gulp来解决的。（不知道大家有没有什么好的方式）

感谢@sharkrice告知  HtmlWebpackPlugin  插件

问题三：构建系统的shim

我们的PC项目（兼容IE8+），依然使用着以前的库，jQuery.js，underscore.js，backbone.js，以及其他第三方不支持commonjs语法的插件，有很多兼容不是很好，最后还是写了另外一个入口文件（包装），然后在webpack合并打包的文件之前引入这些插件，挂载在一个全局的命名空间下，利用新写的一个包装入口导出模块。

问题四：CSS依赖重复

感谢@sharkrice 告知 尝试webpack.optimize.DedupePlugin，问题解决。
