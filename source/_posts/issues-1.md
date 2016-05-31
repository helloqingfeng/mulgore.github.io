title: 我是这样学习前端的
date: 2016-05-16 15:49:38
tags: 前端漫谈
banner: http://o80ub63i5.bkt.clouddn.com/image/issues-1.jpg
---

# 前沿

> 算算时间今年（2016年）是进入前端开发这个领域第五个年头，自从上次总结完《我的编程之路》后，还想从更细节的方向上写一写自己是如何学习前端开发，并且还能够保持进步和对技术的敏感。

对于现在进入这个领域的朋友们来说，很多东西其实你都可以选择放弃了，因为你的起点比之以前要提高了不少，但相对来说知识点又多了很多。PS：*至少你不用去兼容IE6了。*

来看一看JavaScript的趋势图：

> JavaScript 2016年5月 TOP 10

![](https://raw.githubusercontent.com/icepy/_posts/master/img/top20.png)

> JavaScript 趋势图

![](https://raw.githubusercontent.com/icepy/_posts/master/img/index.png)

> Github 2008-2015统计

![](https://raw.githubusercontent.com/icepy/_posts/master/img/github-languages.jpg)

[最流行的编程语言JavaScript能做什么？](http://mp.weixin.qq.com/s?__biz=MjM5Mjg4NDMwMA==&mid=405412226&idx=1&sn=3bc7a9c6afd166591a90723a1802ed99&scene=4#wechat_redirect)

虽然前端领域属于一个比较新的领域，但是至少它也发展了有很多年了。回顾从前，Web前端开发最基础核心的三剑客：*HTML*，*CSS*，*JavaScript*，可能还需要包括*Flash系列*，而现在除了*Flash*（如果你不是直播视频领域的话），基本上还扩充了*HTML5*，*CSS3*，*ES2015*，以及各种框架（backbone,react,angular等）。

<!--more-->

## 角色的定义

前端开发也应该是**软件开发工程**，所以优秀的软件开发工程需要具备的知识，你也应该需要具备。

1. 良好的数学逻辑
2. 良好的数据结构与算法
3. 操作系统
4. 编译原理
5. 计算机系统体系

当你具备良好的基础知识时，对于**编程**二字才可能理解的更透彻。后续你才能进一步的去学习软件设计模式，标准，这些哲学范畴的思想，就好比你认识了汉字，才能阅读完一篇文章。

当然如果你在学校学习的非常好，下列的学习资源推荐就当是复习吧。

**学习资源推荐**

1. 数学逻辑可以观看 *网易公开课* 或者 *iTunes U*
2. 算法或者数据结构，你可以在[https://leetcode.com/](https://leetcode.com/)上刷题来练习
3. 操作系统，我建议你阅读 **《操作系统精髓与设计原理》** 即可，如果理解起来费劲你可以继续去*公开课* 或者 *iTunes U*上搜索视频资源。PS：放心吧，肯定有。
4. 编译原理，推荐在 *iTunes U* 搜索 **冯博琴老师** 的教学视频。
5. 计算机系统体系有非常多的知识点，你可以继续搜索教学视频来观看。

无论何时你都不能丢掉 *HTML*，*CSS*，这个问题在我的身上也出现过，过去很长的时间内我基本不怎么会 *CSS*，这也意味着当我需要去绘制UI时往往效率不高。

其实，这个问题还是牵扯到了如何分配学习资源的问题，欢迎大家来讨论[《JS开发和重构这样的分工是否正确，JS开发者还需要继续深入学习CSS吗？》](https://github.com/icepy/_posts/issues/38)。

如果你的基础知识还不够牢固，我推荐你阅读一下 **角色的定义** ，看一看你需要补全哪些方面的知识。如果你感觉你的基础知识还可以，请往下看：

首先，我推荐大家先阅读一下 [重新介绍 JavaScript（JS 教程）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/A_re-introduction_to_JavaScript) 和 [层叠样式表 (Cascading Style Sheets)](https://developer.mozilla.org/zh-CN/docs/Web/CSS)

**无经验的同学** 我推荐你先使用 [freeCodeCamp](https://www.freecodecamp.com/challenges/learn-how-free-code-camp-works)来学习基础的语法，配合 [JS bin](http://jsbin.com/tixoyutata/edit?html,css,js,console)来练习。另外，你还可以观看 [慕课网](http://www.imooc.com/) 中的 **HTML/CSS/JavaScript入门教学视频**来提高你的运用水平。

**有其他语言开发经验的同学** 我推荐你直接学习一门框架，比如React或者Vue，先做一个小项目，比如React官方提供的To do应用，在这个过程中，你也基本熟悉了JS的语法，使用方式等。你可能还需要 [Devdocs](http://devdocs.io/) 来查看Api，这一点非常重要。

当你渡过了入门阶段之后，如何提升可能会是你目前迫在眉睫的需求。回到Web领域，我们来看它的本质。本质是你所有的工作都在围绕着 **请求** 在处理逻辑。我认为提升的第一步是去研究 HTTP ，当你熟练掌握了 HTTP 以及它身后的 TCP之后，你才会真正理解Web开发的含义。（多线程处理，事件循环，缓存等等这些手段，不都是在如何处理请求么？）没事翻一翻《HTTP权威指南》还是有好处的。

**套用一句老话，如果你的基础不扎实，一切都是“浮云”。**

## 具备良好的视野

> 良好的视野是你能看清楚趋势

如果你现在还准备去学习 *Flash* ，那我只能说你的视野都被狗吃了。至少你可以通过社区来了解 *前端* 的发展动态，去了解出现了哪些新的框架，更新了哪些新的Api或者属性。未来一段时间内，国内或者国际厂商会使用哪些技术等等。

最次一些的，你还可以关注 **Github** 来了解[项目](https://github.com/explore)的趋势。当然，你也可以阅读[https://www.awesomes.cn/rank?sort=trend](https://www.awesomes.cn/rank?sort=trend)

瞧瞧这些年里前端发展的变化：

1. 从框架层面开始：backbone -> angular -> react
2. 工具生态：grunt -> gulp -> webpack
3. 语言：JavaScript 1.3 -> ECMA 5 -> ECMA 2015，CSS2.1 -> CSS3.0，XHTML -> HTML4.0 -> HTML5.0
4. Firefox OS （虽然它挂了）
5. 桌面应用：NW.js -> Electron
6. 出现了Node.js和Mongodb
7. 服务端框架：Express -> koa
8. 移动应用：PhoneGap -> Cordova | ionic -> React Native | weex
9. 语法检查：jslint -> eslint
10. 模块化：AMD | CMD -> Commonjs -> import export
11. 语法增强：CoffeeScript -> Dart -> TypeScript

... **旧技术虽然消亡了，但它们留下的思维启发永在。说不完的变化与发展，拥抱变化用心去体会吧。**

## 戒浮躁，定乾坤

> 随着前端生态圈的繁荣出产了更多的框架和解决方案。

更久远之前我们是这样写前端的：

使用jQuery来编写大量的业务逻辑和效果，圆角我需要四个图来拼接。

2010-2016年的三个阶段：

- 使用*backbone*的MVC组织源代码，大量的使用jQuery插件的形式来构建UI界面，那个年代仅用了*Grunt* 来处理一些合并，压缩的事情。
- 构建工具换成了Gulp，对于业务进行了模块化分层（requirejs），研究angular.js来编写富应用程序。
- 通过组件（react）来构建我们的Web页面，使用webpack来构建模块化和优化，平台向移动迁移，研究React Native这样的混合开发方式，并且使用上了ES6。

生态圈的繁荣也容易让人产生选择困难症，东西越多越难选择，害怕今天刚学习了就被淘汰的心理。这个时候，我想最好的方式就是要戒浮躁，看着东西很多，其实选择一项，也足以。当你成为一个框架的大师时，你还害怕不能成为另一框架的大师吗？专业这个东西除了经验的积累和沉淀，最重要的本质是它们都是互相通顺的。

目前，我选择了研究和使用react这样的生态做为自己的框架技术栈，从中学习也应用在公司的产品中，随着深挖它的源码，反而发现自己对于技术的理解又有了一次提升。

## 做事更要学会思考

对于刚刚参加工作的同学来说，思考比做事更重要。如果你为了业务而业务，不停的去堆积，只能说过些年你还是如此。去好好的想一想，编程到底是在做什么？

**提出问题自问**

1. 怎么才能写好代码，有时候洁癖或者说强迫症很可能会是你的原动力。
2. 是不是该主动的去重构代码
3. 我们需要对于业务代码进行一些分层吗？
4. 我写的代码有没有符合团队制定的编程风格
5. 我是不是该使用语言提供的Api，比如数组中的push，pop等。
6. 公司使用的框架，我理解了吗？

只有自己主动了，去思考了，才可能发现自己的很多问题，有时候自省也非常的重要。

**学习Node**

说真的Node.js在公司内部用于Web开发的场景并不是很多，仅仅是有一些尝试前后分离的项目，体验上来说依然不够友好。但是，这样的一个环境运行时，我认为是有必要学习的。更多的不谈，做为一个技术补充，它也是非常棒的。你可以先阅读 [七天学会NodeJS](http://nqdeng.github.io/7-days-nodejs/) 来入门，至少有一个普遍的了解。其次，建议你学习一个Web开发框架，比如 [koa](https://github.com/guo-yu/koa-guide)，然后，学习一下 [Mongoose](http://mongoosejs.com/) 来驱动数据。

重要的是你应该一无既往的深入学习服务端的思想与知识。

## 坚持写作

> 坚持写作，是沉淀经验的最好机会

所谓的温故而知新，专业在向前发展，接收的大量信息，在人脑中是有局限性的。很多知识，只会存在于一个印象或者一个引子，而写作不仅仅是分享，也是在沉淀你自己的经验。（这一个部分就不浪费篇幅了，我相信做为一个技术专业者，你应该懂的。）

而且写作还能让你和其他开发者针对一个问题展开讨论，何乐而不为呢？

## 提高工作或者学习的效率

提高工作或者学习效率应该是一件非常重要的事情，首先应该需要合理的制定任务与时间，我相信 [trello](https://trello.com/)应该会是一个非常好的工具，来制定Task。

其次，你还应该纪录自己的编程时间，用来了解每天都在编写哪些代码。你可以使用[WakaTime](https://wakatime.com)来分析你的编程。

在学习过程中，你也可以使用[jsbin](http://jsbin.com/)来运行你的代码，观察结果。

最后我认为你需要善用Chrome的书签，将一些资源进行合理的分类。

**推荐列表**

1. [Pocket](https://getpocket.com/)：收集和分类文章资源
2. 在Chrome商店中搜索 **DHC** ，**Postman**，**JSON Editor** ：处理请求测试，JSON编辑和格式化
3. [Web 技术文档](https://developer.mozilla.org/zh-CN/docs/Web) ：火狐提供的Web技术文档，查询的好帮手
4. [http://caniuse.com/](http://caniuse.com/) ：查询CSS3,HTML5的支持度
5. [https://kangax.github.io/compat-table/es6/](https://kangax.github.io/compat-table/es6/) ：查询ES6的支持度
6. [Devdocs](http://devdocs.io/) ：框架文档集合
7. [Travis-CI](https://travis-ci.org/) ：持续集成，我认为如果你善用它，可以帮助你解决很多事情
8. [HTML5 Test](http://html5test.com/) ：打开这个网页可以将你使用的浏览器对HTML5的支持情况打印出来

@guonanci 推荐 [https://github.com/buunguyen/octotree](https://github.com/buunguyen/octotree)

学习任何一门技术，最重要的是要有耐心和恒心，不然一切都是“浮云”。

工具篇，我建议大家阅读[《总结个人2015提高前端效率的方法和工具》](https://github.com/icepy/_posts/blob/master/blog/2015%E5%B9%B4%E5%89%8D%E7%AB%AF%E6%8F%90%E9%AB%98%E6%95%88%E7%8E%87%E7%9A%84%E5%B8%B8%E7%94%A8%E5%B7%A5%E5%85%B7.md)
