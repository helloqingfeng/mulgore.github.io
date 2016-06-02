title: 浅谈音悦台PC端（前端开发）的改造，告别刀耕火种
date: 2016-03-20 11:01:01
tags: 前端漫谈
banner: http://o80ub63i5.bkt.clouddn.com/image/texture-1362879_640.jpg
---

> 架构非一朝一夕，且要紧贴业务。

选择一个行业确实挺难的，特别是对于我们程序员来说。试错的机会，在某些阶段比较容易，但人到了一定的年龄，谨慎会更可靠。加入音悦台，我要做的第一件事情，就是要改造之前PC端的架构。如何去紧贴业务，在改造的过程中又不至于让业务开发停滞，这对于我而言是一件非常大的考量。

## 了解业务与开发方式

**我们的业务**

在开始设计架构之前，我决定先去充分的了解我们的业务特点，音悦台是一家以高清MV视频播放起家的公司，现在它的业务呈现于服务粉丝，包括（商城，V榜）等一系列的产品。就技术场景的特点而言，包括了有PC，Mobile，混合APP，专题页，活动页等等，它涵盖了几乎所有的技术场景，提供了一套服务粉丝的解决方案。

**刀耕火种的年代**

很不幸，在我来之前，我们公司的前端属于“刀耕火种”的年代，所有的代码使用`Spring MVC`来套模板，手机端项目属于WAP站点（也是`Spring MVC`）。如何最小程度的脱离（JSP）或者说最少套JSP模板的架构，是我应该最优先的考量，适量的面向接口，Ajax开发也许会有很大的改变，当然，我所面临的问题还不仅仅是这些。

由于特殊原因，音悦台的前端代码是由各时期的前辈去完成的，几乎都是在赶的状态，hack了很多不一样的功能，每一个时期都风格迥异，可维护性差。对于后来者，就像一根鱼刺咔在喉咙一般，我怎么感觉到灾难，来的这么快呢。

- 缺少统一的项目管理（第三方库随便乱放，光jQuery就有几个地方同时存在）
- 部署困难，没有版本号，而且严重依赖Java环境，编写一个代码就需要重新编译Java重启服务器（配置过于复杂）
- 缺少统一的编程规范
- 很多函数，变量起名很随意，出现了这种`asdf`的变量名
- 几乎没有模块化（除了WAP页使用了requirejs）
- 组件化概念无从谈起，大量的重复代码在搭积木般的堆业务
- 虽然我们使用git版本控制，但是却缺少工作流，大家几乎都往master分支内push代码

## 建模设计与技术选型

> 老板都觉得现在的前端很不科学，很痛苦（因为铁打的营盘，流水的兵。无任何文档沉淀，修改任何东西都非常困难）

变革迫在眉睫，PC端的重新梳理对于我个人而言，成了我很好的练兵之所。于是，我决定将我们公司眼下PC端的需求分解出来。

- 必须完美支持IE8（这个是没办法的事情）
	- 模块化机制的引入，解决如何维护文件
	- 组件化引入，与业务隔离，解决松耦合的复用
	- 不支持编译(js)中间语言（比如TypeScript,es2015）
	- 按需打包，以及自动构建
		- 引入less或者sass，解决CSS的复用
		- 引入PostCSS解决CSS代码的健壮性（比如添加前缀-ms -webkit）
		- 文档沉淀，解决（铁打的营盘，流水的兵）
		- 浏览器自动刷新，项目管理，版本控制
		- 统一编程规范与最佳实践
- 视频行业有其特殊性，必须完美支持与Flash的交互，封装一个统一的SDK
- 必须要支持SEO，（最少程度达标）
- 重新定义发布部署流程

<!--more-->

## 技术选型

根据需求分解的特征进行选型，所有的子项目都依赖于`完美支持IE8`，所以对于我的选择局限性就比较大了。

**Vue.js**

- [Vue.js unit tests](http://vuejs.org/unit/)
- [Vue.js Sauce Labs](https://saucelabs.com/u/vuejs)

unit tests在IE上跑不起来，我所认知的结果是：不支持IE8

**React.js**

虽然FB提供了运行在旧浏览器上的解决方案：[Working With the Browser](https://facebook.github.io/react/docs/working-with-the-browser.html)，但是，对于未来，博客上明确书写了将不在支持，可查看[Discontinuing IE 8 Support in React DOM](https://facebook.github.io/react/blog/2016/01/12/discontinuing-ie8-support.html)，后来我在Github上找到一个[react-ie8](https://github.com/xcatliu/react-ie8)项目，对于商业公司而言，这个解决方案还是有很大的风险，于是：放弃。

**Angular1.x**

对于即将到来的Angular2.x以及Angular1.x庞大而臃肿的身躯（总不能我的专题页，活动页也用上Angular1.x吧），这是我最快放弃考察的一个项目。

## 那么问题来了，我该怎么办？

对于基础库而言，我选择了老三项，对于一个既需要复杂业务模型（复杂交互类型的页面），又有适当简单的特点业务（活动页面），MVC分层将有助于我们分解业务编程。而且，这些也有足够的中文资料，以及文档让团队中（没有接触过MVC）的同学去学习和适应。

- [jQuery 1.x]()
- [underscore]()
- [backbone]()

当然到这里我们的设计还远远不够，我们还缺少模块化，组件化，以及对backbone适当的改造。首先，我必须对开发方式进行隔离，分为了dev和build两个环境（当然，它是我既定的目标），以及引入一些表现良好的工具来辅助开发（比如browser-sync自动刷新页面）。为了更好的管理项目以及优化代码，我选择了npm系统来管理我的第三方依赖，npm脚本钩子来帮助我执行start，dev，build，test等环境，以及webpack来完成系统内的模块化构建。老实说，首先我们用它解决了js模块化的问题，至少commonjs的风格看起来可以保持一致（但是我还需要去协助大家避免循环引用），然后处理按需打包的问题（至少很长一段时间里我们的PC端还将是传统的页面而不是webapp）。

关于webpack的应用以及多资源打包，推荐大家阅读我的另一篇文章：[webpack在PC项目中的应用](https://github.com/icepy/_posts/issues/25)

### 目录结构设计

对于传统的项目（`Spring MVC`），我们进行了一些适当的改变。当然，我们总体的目标，是在向面向接口开发来靠近。

	Project_dev  根目录
		dist 经过编译之后可发布的目录
		flash 内部swf文件放置的目录
		link 内部自己开发或者未兼容Commonjs的库（未建立私人NPM服务仓库）
		static 切图的静态页面放置的目录
		web 入口页面（用户访问的地址）
		test 单元测试
		img 图片资源
		mock 本地模拟数据
		cross-url 跨域url（兼容老Spring MVC）
		js //经过webpack打包之后的文件
		src //js源文件
			view 视图目录
				index  业务模块
					topbar.view.js
			model 模型目录
				index
					topbar.model.js
			template 模板
				index
					topbar.html
			config.js	//项目配置文件
			index.main.js  //入口文件
		style
			css //less编译之后的文件
			less //less源代码文件
			reset.css  //公共文件
		.eslintrc
		.gitignore
		README.md
		gulpfile.js
		package.json
		map.json
		tools.js //提供的工具，快速生成view，model文件
		webpack.dev.config.js

最后可发布的目录结构：

	Project_build

		js //处理过后的js文件
		style //处理过后的css文件
		web //用户访问的真实页面
		link //处理过后的第三方库或内部自己开发的库
		flash //swf文件
		cross-url //兼容（Spring MVC）的跨域

对于我们的git则启用了一个基础的git flow工作流，避免大家push到master分支，每一次的发布都必须有足够的备份。

### 第三方库整合

针对第三方库的整合是规避了一些基础控件（除非有自己研发的需求），列表如下：

- Swipe（轮播图）
- 腾讯云 SDK
- artTemplate
- amazeui（参考较多）

### backbone改造

原始的`backbone`并不能很好适应我们的业务产品，它虽然有backbone.Router，但是却缺少基于路由的生命周期，它的Model也不是很健壮（可配置性以及数据的本地缓存），当然它的View是我们经常要使用的，但是却缺少相应的钩子方法，于是对于它们适当的改造，有助于公司产品的业务开发（便捷）以及稳定性。

- baseView
- baseModel
- baseRouter

`baseView`实现了相应的钩子方法，比如`rawLoader`，`beforeMount`，`afterMount`，`ready`等，对于参数传递也有了一些规范性的定义，比如：

```JavaScript

{
	"props":{},  
	"methods":{},
	"state":{}
}


```

UI渲染依赖的数据通过`props`传递，外部可能用到的方法通过`methods`传递，内部需要维护的状态可以通过`state`传递，规范参数的目标是对一些写法进行约束，在排错时可以更容易定位到错误。

`baseModel`除了实现了一个beforeEmit钩子外，基本上扩展和包装了一些便捷的存取方法，比如`$get`，`$set`，`$filter`，`$sort`，以及发送请求的便捷方式。

`baseRouter`主要是实现了基于路由的生命周期（为了webapp准备的，可能未来会有要求兼容IE8的Webapp）。

### 编写组件

组件化从开发的角度来看，由于每个组件的相对独立性，开发者在开发期间不会产生依赖冲突，只需专注于自身的模块开发，提高开发效率；从维护的角度来看，于模块相关的资源均组织在一起，十分便于维护和整理。对于组件，我们进行了一些额外的处理，一个组件最少需要包含template.html以及index.js两个文件，比如：

	loginBox //目录
		template //目录
			close.html
			login.html
		index.js


我们的css文件放置在style目录下，它是一个less文件，当业务编程需要时，自己在自己的业务less文件中`@import url('common/footer.less');`即可，毕竟我们最终需要一个link css文件，而不是内嵌在html中，webpack帮助我们在dev环境中，既对这些东西进行了处理。

在index.js文件中，只需要根据我们指定好的一些规则书写即可：

规则一，继承baseView的组件

```JavaScript

var BaseView = require('BaseView');
var closeTemp = require('./template/close.html');
var loginTemp = require('./template/login.html');
var LoginBox = BaseView.extend({
	events:{

	},
	beforeMount:function(){

	},
	afterMount:function(){

	},
	ready:function(options){
		var props = options.props;
		var state = options.state;
		var methods = options.methods;
	}
});
var shared = null;
LoginBox.sharedInstanceLoginBox = function(options){
	if(!shared){
		shared = new LoginBox(options);
	};
	return shared;
};
module.exports = LoginBox;

```

规则二，不继承baseView的组件

```JavaScript

var closeTemp = require('./template/close.html');
var loginTemp = require('./template/login.html');
var LoginBox = function(options){
	var props = options.props;
	var state = options.state;
	var methods = options.methods;
};
var shared = null;
LoginBox.sharedInstanceLoginBox = function(options){
	if(!shared){
		shared = new LoginBox(options);
	};
	return shared;
};
module.exports = LoginBox;

```

个人非常建议给每一个类配置一个单例选项，这非常有用。

### Flash SDK

如何统一的与Flash交互，也是我们需要考虑的方向。第一版的简化，在很短的时间内做了出来。主要用来区分IE和非IE的情况，IE下只识别object标签，而非IE只识别embed标签。每一个Flash注入的方法，为了方便业务开发，都进行了封装，目标是：调用简单。

### 进入愉快的业务编程阶段

在前期的准备工作完成之后，我们针对某一项业务进行了Test编程。

一个PC站点的界面基本上是由header,content,footer构成的，在header中可能还有一些其他的业务，这些我们不管，针对具体的业务，我们需要进一步的分析界面的构成，在进入编程阶段之前，良好的分析会对进度有良好的帮助。

是的，分析应该是你要做的第一件事情。

我提供了一个tools.js脚本用于快速的生成view，model文件，大量重复性的代码，将由工具来辅助生成，业务编程将更专注于业务。

其实最后一步，愉快的进行编程即可，运用你熟悉的jQuery API配合一些base API，轻轻松松完成了业务编程。

（PS：当然也提供了mocha chia sinon的demo，来对业务进行自动化测试，毕竟测试用例还是需要业务来编写和维护，所以考虑了上述情况之后决定：业务可选，核心包未来必须补上。）

### 构建可部署文件的脚本

虽然我们的dev环境使用webpack来进行处理，但是它还不是我们最终想要发布的资源（首先，我希望发布目录是一个非常干净的dir，其二一些配置文件不应该出现在发布目录中，以及对.html进行hash处理）。webpack在这方面还是有些欠缺，所以最后的可部署文件，我们使用gulp来进行最后的处理：


```JavaScript

// 清理dist目录
gulp.task('clean', function () {
  // content
  return gulp.src(['./dist'], {read: false}).pipe(clean());
});

gulp.task('build:rename',['build:clean'],function(){
    return gulp.src('./dist/temp/*.html')
        .pipe(gulp.dest('./dist/web'));
});

gulp.task('build:clean',['build:retemp'],function(){
    return gulp.src('./dist/web/*.html',{read:false})
        .pipe(clean());
})

gulp.task('build:retemp', ['build'], function () {
  return gulp.src('./dist/web/*-*.html')
    .pipe(rename(function(path){
        var basename = path.basename.split('-');
        if (basename.length > 1) {
            basename.pop();
            path.dirname = '/temp'
            path.basename = basename.join('-');
            path.extname = '.html';
        }
    }))
    .pipe(gulp.dest('./dist'))
});

//进入build
gulp.task('build', ['build:move'], function () {
  var cssFilter = filter('./dist/style/*.css', {
    restore: true
  });
  var jsFilter = filter('./dist/js/*.js', {
    restore: true
  });
  var date = new Date();
  var times = date.getFullYear() + '-' + (date.getMonth() + 1) + '-' + date.getDate() + '   ' + date.getHours() + ':' + date.getMinutes() + ':' + date.getSeconds();
  var banner = [
    '/**',
    ' * @project <%= pkg.name %>',
    ' * @description <%=pkg.description%>',
    ' * @version v<%= pkg.version %>',
    ' * @time ' + times,
    ' * @author <%= pkg.author %>',
    ' * @copy <%= pkg.homepage %>',
    ' */',
    ''
  ].join('\n');


    function htmlMaped (filename) {
      return filename.replace(/[-][\w]{10}.html/g, '.html');
    }

  return gulp.src('./dist/web/*.html')
    .pipe(useref({
        noAssets:false
    }))
    .pipe(cssFilter)
    .pipe(cssFilter.restore)
    .pipe(jsFilter)
    .pipe(jsFilter.restore)
    .pipe(rev())
    .pipe(revReplace({
        modifyReved: htmlMaped,
        modifyUnreved: htmlMaped
    }))
    .pipe(useref())
    .pipe(gulpif('*.js', header(banner, {pkg: pkg})))
    .pipe(gulp.dest('./dist/web/'))
});

gulp.task('build:move', ['clean'], function () {
  // content
  var dontMovePath = '!./';
  var movePath = './';
  return gulp.src([
      movePath + 'link/base.library.js',
      movePath + 'link/webim.js',
      movePath + 'link/json2.js',
      movePath + 'img/**/*.*',
      movePath + 'web/*.*',
      movePath + 'flash/*.*',
      movePath + 'style/**/*.css',
      movePath + 'js/*.js'
    ], {base: '.'})
    .pipe(gulpif('*.js',uglify({
        compress:{
            pure_funcs:['console.log','warn']
        }
    })))
    .pipe(gulpif('*.css', autoprefixer({
      browsers: ['last 2 versions', 'Android >= 4.0'],
      cascade: true, //是否美化属性值 默认：true 像这样：
      //-webkit-transform: rotate(45deg);
      //        transform: rotate(45deg);
      remove: true //是否去掉不必要的前缀 默认：true
    })))
    .pipe(gulpif('*.css', minifycss()))
    .pipe(gulp.dest('./dist/'));
});
```
## 收尾工作

编写文档（打算在API文档上利用JSDoc自动生成），也许还是要手工编写？主要我是想支持md格式的文件，这样将来好在我们的git系统中，可以很好的阅读。

另外我们启用了eslint来进行语法检查，以及对于编程规范，考察了[airbnb/javascript](https://github.com/airbnb/javascript/tree/master/es5)和[airbnb/css](https://github.com/airbnb/css)，请原谅我偷懒，我是真觉得airbnb的规范非常赞～。

## 未来，我们仍然在路上

对于前端发展的探索，我们依然在路上。技术的变革，对于用户（可能感知不到），对于开发者而言，更健壮的程序，将让用户更明显的感受到体验的好坏。前端这些年的变化，还是需要每一个人自我驱动的去学习与适应。PC端的架构改造，即将告一段落。未来，将有更极致的挑战（移动和混合应用的架构设计，FE的探索{React React Native}，以及Node.js在公司产品中的落地，也许会是我们前端的CI系统，CSS动画研究，Video视频和画布方面的研究。）

我们需要优秀的开发者加入，一起来完善这些，有兴趣的朋友，可以将简历发送到xiangwenwe@foxmail.com，期待～。

# 更新部分 （2016-05-09）

应用在PC端上的项目结构整理出来，可访问：[https://github.com/sapling-team/generator-sapling-pc](https://github.com/sapling-team/generator-sapling-pc)

*注明：我们需要兼容IE8，所以使用的还是backbone。如果你追求的是更新的技术栈，我觉得你可以参考一下其他的特性，不包括backbone部分的东西。*

------------------- 分割线---------------

**团队编码风格检查**

对于开源社区的编码规范，我考察了一系列的，比如Google，微软，Facebook，不过最后我们还是采用了`airbnb`的编码规范，我也真心的建议大家可以认真的阅读阅读。[https://github.com/airbnb/javascript/tree/master/es5](https://github.com/airbnb/javascript/tree/master/es5)，另外为了达到在`commit`期间进行一次检查，我们使用了git hook来做eslint检查，通过才允许提交。

如果你写不了`shell`脚本，那你可以使用[https://github.com/typicode/husky](https://github.com/typicode/husky)这个项目。

**快速生成的view，Model文件的工具**

考虑到很大一部分初始化的代码，需要重复编写，比如：

```JavaScript
'use strict';

var base = require('base-extend-backbone');
var BaseView = base.View;

var View = BaseView.extend({
  el: '',
  rawLoader: function () {
    return '';
  },
  context: function (args) {
    console.log(args);
  },
  beforeMount: function () {
    //  初始化一些自定义属性
  },
  afterMount: function () {
    //  获取findDOMNode DOM Node
  },
  ready: function () {
    //  初始化
  },
  beforeDestroy: function () {
    //  进入销毁之前,将引用关系设置为null
  },
  destroyed: function () {
    //  销毁之后
  }
});

module.exports = View;
```

所以，我们专门写了一个小脚本来辅助创建`view`，`model`文件，以提高工作效率。

**第三方管理**

我们使用`bower`+`npm`来管理所有的第三方资源，`bower`用在管理`jQuery`以及其自身的插件生态中，`npm`则是用来管理其他的库和自己维护的库文件。\

**include**

`include`的特性在做不是单页Web应用时很有用，如果100个页面都要同时复制100百次相同的头部和尾部的话，那我也会疯的。所以，我们启用了`jade`来代替了`HTML`，并且和`html-webpack-plugin`插件配合使用。

**compile.config**

这个`config`配置文件主要用于在jade中编写的逻辑脚本，可以在某些情况下替换一下地址（比如CDN）,另外在一些动态化的场景中，也可以读取这个配置文件来知晓资源依赖。

**目录结构**

我们的目录结构是一个典型的`web app`结构：

```HTML
app/
 images/
 link/
 stylesheets/
 src
 web/
```

`images`主要用于放置图片，`link`用于管理第三方资源（ bower下载的资源放置在这里），`stylesheets`放置`sass`源文件，`src`则是放置js源文件，`web`放置jade文件。src中我们又分为了`views`，`models`，`module`三个目录，顾名思义这些是放置视图，模型，模块的目录，我们的入口文件与它们平行同级。

为了`getEntry`函数可以正确的提取入口依赖名，我们采用了一致命名的原则。比如我们有一个`index`页面。

- 在web中放置`index.jade`
- 在src中放置`index.js`入口文件
- 在src的views和models中分别创建`index`目录
- 在src的views和models的`index`目录中创建`index`页面所需要的视图和模型
- 在stylesheets中创建`index`目录和`index.sass`入口文件
- 在src的`index.js`入口文件中`require('../stylesheets/index.sass')`文件

启动`npm run dev` webpack即可
