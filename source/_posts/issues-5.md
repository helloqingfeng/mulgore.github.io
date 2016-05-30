title: 使用Travis-CI+Coveralls让你的Github开源项目持续集成
date: 2016-04-7 18:34:04
tags: 工具
---

> 我在成长为最好的自己；

对于经常浏览`Github`的朋友来说，是不是会经常看见下图这样的状态图标？

![](https://raw.githubusercontent.com/icepy/_posts/master/img/build-state-icon.png)

## 这是什么？

Travis-CI是一个持续集成构建项目，使用了小清新的yml语法，你可以把它想象成一个Web的linux系统，通过yml语法来驱动执行。（你可以这么想象），它对于Github上的开源项目是免费的，当然private repo 就有些小贵了。

Coveralls是一个自动化测试覆盖率的服务。

在使用这两个服务之前默认你已经熟练操作Github了，接下来我将使用`Mocha`，`Chai`来构建一个自动化测试。

<!--more-->

## 准备工作

使用你的Github账户 login [https://travis-ci.org/](https://travis-ci.org/)，点击右上角的`Accounts`到达一个页面，点击其中的`Sync account`，同步一下Github上公开的仓库。其实Travis-CI的官网已经给出了详细的步奏，接下来就是选择你要持续集成的项目，打开开关，创建配置文件，触发第一次钩子。

![](https://raw.githubusercontent.com/icepy/_posts/master/img/sync-repo.png)

    npm install mocha chai --save-dev --verbose


使用你的Github账户 login [https://coveralls.io/](https://coveralls.io/)，点击右上角的`ADD REPOS`，开启一个仓库。

    npm install coveralls --save-dev --verbose

*备注：所有的例子运行于Mac，windows系统可能有些差异*

## 开始

在项目的根目录创建`.travis.yml`文件，并且书写描述信息：

```yml
language: node_js
sudo: true
node_js:
  - '4.1.1'
cache:
  directories:
    - node_modules
before_install:
    - npm install
script:
  - npm test
  - npm run dev
  - npm run build
  - npm run checkdir
after_script:
  - npm run coverage
```

我们创建的是一个JavaScript项目，所以`language`选择`node_js`，并且选择一个版本。`Travis-CI`默认是会执行`npm test`的，所以我们还需要保证`npm test`可用。关于NPM SCRIPT HOOK的使用，推荐大家阅读[《npm-scripts》](https://docs.npmjs.com/misc/scripts)。

`Travis-CI`对于执行环境它提供了一个对应的`生命周期`，比如`before_install`，`after_script`等，这个周期是为了方便每一个命令的前后顺序可以确保正确。当然对于我们而言最重要的地方在于`script`，它是一个顺序的执行，（一行执行完再执行一行）。

接下来我们还需要安装一下 [istanbul](https://github.com/gotwarlost/istanbul)，它可以生成`LCOV`文件，这是[https://coveralls.io/](https://coveralls.io/)服务所需要的东西。

    npm install istanbul --save-dev --verbose

*注意事项：istanbul mocha coveralls 最好不要全局安装，可能会出现莫名其妙的问题*

以`ManagedObject`类为例子：

```JavaScript
var chai = require('chai');
var Manage = require('../../src/entity/ManagedObject');
var expect = chai.expect;
// 省略....
describe('ManagerObject', function() {
    // 省略....
    describe('$sort 对实体内部的某项数据进行排序，第二个参数是要排序数据的.结构化表达式，第二个参数是排序的根据',function(){
        it('测试.表达式< 降序',function(){
            expect(manager.$sort('items','id.<')).to.eql(sortTestData)
        })

        it('测试.表达式> 升序',function(){
            expect(managerT.$sort('items','id.>')).to.eql(sortTestDataT)
        })

        it('测试 function进行排序',function(){
            expect(managerT.$sort('items',function(){
                return true
            })).to.eql(sortTestDataT.reverse())
        })
    })
})
```

编写npm scripts hook：

```JavaScript
    "scirpts":{
         "test": "node ./node_modules/.bin/istanbul cover ./node_modules/mocha/bin/_mocha --colors ./configs/mocha/**/*.test.js",
         "coverage": "cat ./coverage/lcov.info | coveralls"
    }

```

- node ./node_modules/.bin/istanbul cover 调用istanbul执行cover命令（默认生成LCOV文件）
- ./node_modules/mocha/bin/_mocha --colors ./configs/mocha/**/*.test.js 关联mocha测试驱动程序，执行configs/mocha目录下的后缀是.test.js测试用例文件_
- cat ./coverage/lcov.info 在终端打印
- coveralls 执行coveralls服务（如果是公开的项目，不需要集成任何token在.travis.yml文件中）

## 最后

    git push origin master

提交一次commit来触发`Travis-CI`，你就能得到下图了：

![](https://raw.githubusercontent.com/icepy/_posts/master/img/travis-ci.png)

![](https://raw.githubusercontent.com/icepy/_posts/master/img/coveralls.png)

在构建的过程中，你可能还需要注意一些问题：

- yml格式需要书写正确（确保每个space都必须要正确，travis-ci提供了一个检测工具），其实提交到Github之后如果你的yml格式没有写对，是不会有style的。
- 使用脚本的时候不要使用watch模式，这样会构建失败
- 对于npm安装，不需要打印安装信息（因为log太多也会构建失败）

当然`Travis-CI`还有许多更高级的用法，需要大家一一去挖掘了。
