title: Webpack实践最后一篇
date: 2016-04-14 18:20:07
tags: 工具
---

> 温故而知新

这应该是目前这个阶段最后一篇关于webpack的实践经验，也许你会学习到该用怎样的思想去使用webpack，也许你会认为这是一坨屎一样的文字。不过，我会尽量的描述，我们的实践以及给出一份在Mac和Win下的Demo，这个项目也是我们应用在PC端的实践，访问[https://github.com/sapling-team/generator-sapling-pc](https://github.com/sapling-team/generator-sapling-pc)来阅读我们PC端的脚手架吧。（完美兼容IE8+）

我们对`backbone`也提供了一份有用的扩展，可访问：[https://github.com/sapling-team/base-extend-backbone](https://github.com/sapling-team/base-extend-backbone)

更多基础的配置信息，建议大家阅读[《webpack在PC项目中的应用》](https://github.com/icepy/_posts/issues/25)

<!--more-->

## 该用什么样的思想来使用

> 你用的始终还是Node.js环境

我觉得你应该要忘记webpack，因为你使用的终归还是Node.js。为什么这么说，因为如果只使用简单的配置，你可能只把它做为一个模块加载器。如果你熟练的使用了Node.Js那么恭喜你，我们将用环境的思维来使用它，并用Node.Js来驱动你的构建流程。（NPM可以使用的模块，你都可以在构建环境中使用）

首先，我们应该将脚本文件放置在bin目录下，这是unix编程的常识。接着，对于你的产品，定义两个环境：dev和product，在NPM Scripts中使用`NODE_ENV=dev webpack --config bin/webpack.config`，然后在脚本文件中使用`process.env.NODE_ENV`来获取环境变量并区分执行。（很不幸的是，Win用户无法获取ENV，所以你需要建立两个文件来做dev和product）

## 抽象dir

抽象你的目录资源，设计一定的规则，可以进行批处理配置信息。

	npm install glob --save-dev --verbose

我们的入口文件都放置在src中，根据业务特点来命名，比如：`index.js`，`code.js`。

```JavaScript
var path = require('path');
var glob = require('glob');

module.exports = getEntry;

function getEntry(sourcePath){
    var entrys = {};
    var basename;
    glob.sync(sourcePath).forEach(function(entry){
        basename = path.basename(entry,path.extname(entry));
        entrys[basename] = entry;
    });
    return entrys;
}
```
在`webpack.dev.config.js`文件，可以通过getEntry函数来统一处理入口，并得到`entry`配置对象。如果你是多页面多入口的项目，建议你使用统一的命名规则，比如页面叫`index.html`，那么你的js和css入口文件也应该叫`index.js`和`index.css`。

## include

> 在编译期来决定最终呈现什么样的HTML

在后端语言的模板中`include`是一个非常有用的特性，因为它可以抽象分离不同的HTML结构，来达到复用的目的。

	npm install jade-loader --save-dev --verbose

```jade
doctype html
html(lang="en")
    head
        - var titleValue = htmlWebpackPlugin.options.title
        title=titleValue
        meta(charset="UTF-8")
    body
        include common/header
        include index/container
        include common/footer
        include common/lib
```

```jade
- var src;
- var map = ['jquery/dist/jquery.min.js','underscore/underscore-min.js','backbone/backbone-min.js']
if htmlWebpackPlugin.options.cdn
    - src = 'http://127.0.0.1:3000/www/link/'
else
    - src = '/link/'
each val in map
    script(type="text/javascript",src=src+val)
```
```JavaScript
{
    test:/.jade$/,
    loader:'jade-loader',
    exclude:/(node_modules)/
}
```
不仅如此`jade`还可以做更高灵活的配置。

## html-webpack-plugin

如果你是多页面，或者单页应用都需要这个插件来帮忙处理HTML的内容，比如上述的jade模板的`include`。

```JavaScript
var pages = getEntry('./app/web/*.jade');
for(var chunkname in pages){
    var conf = {
        cdn:false,
        filename:chunkname+'.html',
        template:pages[chunkname],
        inject:true,
        minify:{
            removeComments:true,
            collapseWhitespace: false
        },
        chunks:['common',chunkname],
        hash:false
    }
    plugins.push(new HtmlWebpackPlugin(conf));
}
```
根据`抽象dir`的方法，我们可以通过`getEntry`来获取一个pages对象，并使用chunks来处理每一个入口页面的依赖。

如果你是单页应用，你只需要添加一次HtmlWebpackPlugin插件即可。

## 如何优化

> 优化，但不要过度的优化

运行`npm run product`来构建你的发布资源。

过度优化的结果：

```Less
.title {
  margin-left: auto;
  margin-right: auto;
  .size(margin-top, 15px);
  .size(margin-bottom, 15px);
}
```
最后经过webpack的优化变成了：

```CSS
.title {
  margin: 2.6408vh auto 2.6408vh;
  margin-top: 1.5rem;
  margin-bottom: 1.5rem;
}
```
那么问题来了Android4.3以下版本是不支持vw,vh单位的。

我认为对于一个项目，优化的方式应该可以从下列的几个点中去挖掘：

**externals 分离**

善用`externals`将过大的文件分离出去，然后再用`script`标签引用即可。

**alias机制**

给一些模块启用别名，来提高webpack的搜索速度。

**将某些对象暴露在全局**

- 在loader中使用`expose`将对象暴露出去`loader: 'expose?jQuery'`
- 使用`ProvidePlugin`插件的帮助

```JavaScript
new webpack.ProvidePlugin({
    "M": "mock",
}),
```
假设`mock`对象中有`set`，`get`方法，那么此刻你可以在使用`M.set`，`M.get`方式来调用。

**启用热替换**

- 将代码内敛到入口js文件中，然后启动`webpack.HotModuleReplacementPlugin`插件
- 使用自带的`webpack-dev-server`启动一个服务器
- 直接在`webpack.config.js`文件中配置

```JavaScript
//配置

devServer:{
	port:4000,
	contentBase:'./app',
	historyApiFallback:true
}

```
**合理的使用CommonsChunkPlugin将每一个chunk分割开，并提取**

`CommonsChunkPlugin`有一些属性，比如`minChunks`可以设置如果`require`几次再提取的问题。

**个别项目环境变量优化**

定义你的`process.env.NODE_ENV`变量，启用`DefinePlugin`插件来优化警告信息，很多框架，比如react都带有警告，比如：

```JavaScript
if(process.env.NODE_ENV !== 'production'){

}
```

在prodcut期，我们可以通过`DefinePlugin`来打入环境变量来将这些剔除。

```JavaScript
plugins.push(new webpack.DefinePlugin({
    'process.env':{
        'NODE_ENV':JSON.stringify(process.env.NODE_ENV)
    }
}));
```

构建期间`process.env.NODE_ENV !== 'production'`会变成：

```JavaScript
if(false){

}
```

压缩工具，会忽略false内的内容，你会发现体积将减少了很多。

## 清理工作

每次`npm run product`之后，因为设置了`hash`属性，所以会生成不同的文件，那么问题来了，我无法清理www目录，那么这时候，就可以换到Node.js的文件系统上了。

```JavaScript
var fs = require('fs');
var path = require('path');
var containerPath = path.resolve('./');

module.exports = rmdir;

function rmdir(dirPath){
    dirPath = path.resolve(containerPath,dirPath);
    var dirs = [];
    collection(dirPath,dirs);
    dirs.forEach(function(v){
        var status = fs.rmdirSync(v);
        if(status){
            console.log(status);
        }
    });
}

function collection(url,dirs){
    var stat = fs.statSync(url);
    if(stat.isDirectory()){
        dirs.unshift(url);
        recursion(url,dirs);
    }else{
        if(stat.isFile()){
            fs.unlinkSync(url);
        }
    }
}

function recursion(url,dirs){
    var arr = fs.readdirSync(url);
    var i = 0;
    var le = arr.length;
    for(;i<le;i++){
        var v = path.resolve(url,arr[i]);
        collection(v,dirs);
    }
}
```

## 运行脚手架Demo

你可以下载脚手架项目跑一跑：[https://github.com/sapling-team/generator-sapling-pc](https://github.com/sapling-team/generator-sapling-pc)

最终的发布可能还是需要gulp的一些辅助，不过这不要紧了，它只是帮助我们挪动了一些文件，最终成为了一个`www`目录。
