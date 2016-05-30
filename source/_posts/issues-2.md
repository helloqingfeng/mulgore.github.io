title: 玩转NPM
date: 2016-04-27 18:13:44
tags: Node.js
banner: https://www.npmjs.com/static/images/npm-logo.svg
---

自从转向了Node开发之后，对于NPM的熟悉程度越来越高，这一篇文章希望可以让大家都能“玩转NPM”。

做为Node世界里的包管理器，我想大家从Grunt时代起就已经熟练的使用npm install命令来安装一些依赖完成前端自动化构建任务。但是，你真的了解它么？package.json文件中常常记录了大量的信息，有哪些是你必须要有的元数据呢？大部分人都会使用npm install react --save-dev来写入package.json文件。（但很不幸，这是不对的）

## 正确的区分环境依赖

首先我想说明的是package.json对于NPM来说是非常重要的元数据集，对于我们的应用来说，package.json文件使用`dependencies`和`devDependencies`来定义应用依赖和开发环境依赖，如果你还无法搞清楚这些，我建议你继续往下阅读。对于一个Web应用来说你的自动构建任务所依赖的包，它属于开发环境依赖，如果你的Web应用依赖jQuery来发起Ajax请求，它应该属于应用依赖。

    npm install webpack --save-dev
    npm install jquery --save

应用依赖和开发环境依赖是有区别的，因为如果你的Web应用依赖的jquery写入到了开发环境依赖中，它是无法更新的，除非你手动修改package.json文件中的版本信息，并且将`node_modules`中的jquery删除和重新使用npm install安装之外，你别无它法。正确的姿势是写入应用依赖，并且用`npm update  jquery --save`方式来更新你的jquery。

<!--more-->

## NPM 命令系统

学习如下的常用命令，你可以很快的上手熟悉npm。

**如何使用帮助信息**

熟悉常用的帮助命令有助于你了解npm，输入如下：

    npm help install

这个命令可以获取`install`命令的详细信息。

你也可以输入：

    npm -l

来查看各命令的简单用法。

**初始化package.json**

    npm init

关于`package.json`文件中元数据的定义，推荐大家阅读一个翻译版本：[npm的package.json中文文档](https://github.com/ericdum/mujiang.info/issues/6/)

**搜索与查询**

你可以使用`search`命令来搜索`npm`仓库

    npm search jquery

我该如何去查询jquery的信息呢？你可以使用npm info jquery来查询jquery，比较悲剧的是信息量非常大，也许不是太适合阅读，如果你有过linux编程或者使用过linux的经验，那么你肯定知道pipe（管道）这样的东西，|做为管道符，利用它可以做很多事情。

    npm info jquery | grep 1.12.3

最直接的方式是你使用npm dist-tag ls jquery使用它可以查询jquery的版本，如果你想按照特定的版本使用@符合连接版本号即可，比如这样npm install jquery@1.12.3。

**全局安装**

安装全局的包，比如我们经常使用的nodemon可以进行全局安装。

    npm install -g nodemon

这与局部安装只是多了一个-g而已。

**删除**

有安装那么肯定有对应的删除，使用npm uninstall jquery来删除你的jquery。

**更新**

你可以先使用`npm outdated`来检查当前安装的所有npm包是否有更新，如果有列出信息，则说明需要更新了。如果无，则不需要任何更新。

更新时你可以使用`npm update`命令来更新所有可更新包，如果你只想更新某个具体的包，只需要输入如下：

    npm update jquery

**发布**

如果你想发布一个包到NPM，首先你需要注册一个账号，你可以在[网站](https://www.npmjs.com/)上注册，也可以使用npm adduser命令（如果你是在网站上注册的，在发布之前需要记得在命令行中再输入一次npm adduser并且输入你的账号，密码和邮箱）

然后使用npm publish来指定一个目录来发布，如果没有发布成功（说明你想发布的包名字被人注册了）。

**更新已发布包的版本信息**

当我修改了包中的一些文件后再次发布需要你修改package.json文件中的version信息（手动修改也不是不行），当然你可以使用相应的命令来完成这些。

npm version patch修改版本信息中第z位的数字+1。

npm version minor修改版本信息中第y位的数字+1，并且重置第z位的数字为0。

npm version major修改版本信息中第x位的数字+1，并且重置第y位和第z位的数字为0。

**废弃已发布的某个版本的模块**

自从出现了`npm `事件之后，想删除模块并不是那么简单了。不过，好在你可以使用`npm deprecate`命令来废弃你某个版本的模块。

    npm deprecate base-extend-backbone@"<0.1.4" "bug fixed in v0.1.4"

这样当用户安装小于`v0.1.4`版本的模块时，会在命令行中得到一行警告信息。

**link**

在开发npm包的时候，有时候你期望可以边开发边试用，你知道的常规情况下使用一个模块，需要将它安装到`node_modules`目录中。`link`命令就是可以这样方便的让你可以在开发中使用你的模块。

假设你有一个`src/githubApi`模块，首先你需要在模块目录中运行`npm link`命令。

    cd src/githubApi
    npm link

这个时候`githubApi`已经可以全局调用了，因为`npm link`命令帮助我们在npm的全局模块目录中生成了一个符号链接文件。

现在cd到你的项目目录中，假设你有一个这样的项目`blog/`

    cd blog/
    npm link githubApi

现在你可以在项目中加载`githubApi`模块了：

```JavaScript
var githubApi = require('githubApi');
```

如果你不需要它的时候，你可以通过`unlink`来删除，比如

    cd blog
    npm unlink githubApi

## NPM SCRIPTS系统

npm不仅可以用于模块管理，也可以执行相应的脚本，在`package.json`文件中`scripts`可以定义一些脚本命令，让npm直接调用。

```JavaScript
"scripts": {
    "test": "mocha --colors test/*.spec.js",
    "start": "gulp server",
    "dev": "webpack --watch --colors --config bin/webpack.dev.config.js",
    "product": "gulp build",
    "preproduct": "webpack --colors --config bin/webpack.product.config.js --optimize-minimize",
    "eslint": "eslint app/src/ app/stylesheets",
    "precommit": "npm run eslint"
}
```

现在你可以直接使用`npm run dev`来执行`webpack`的构建任务。

可喜的是npm run 为每一个命令都提供了`pre-`和`post-`两个钩子（hook），有了这些其实，你可以做很多事情，比如`precommit`，在git commit之前先通过eslint检查，如果检查未通过直接阻止commit。推荐大家使用[https://github.com/typicode/husky](https://github.com/typicode/husky)项目来为团队指定一些初级的review。

最有意思的是，你还可以使用`package.json`文件中的内部变量，使用`$npm_package_xxx`的方式来引用。


## 最后

俗话说：师傅领进门，修行看个人。更多NPM的信息，就要看大家平时在使用的过程中慢慢去积累了。最后，再给大家推荐一个npm 镜像：[https://npm.taobao.org/](https://npm.taobao.org/)
