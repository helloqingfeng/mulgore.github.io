title: Swift的期待
date: 2016-04-16 18:17:36
tags: Swift
---

> 会当凌绝顶，一览众山小。

**摘要：去年底苹果开源 Swift 之后，Google、Facebook和Uber三个互联网巨头就曾在伦敦召开会议讨论Swift在各自开发战略中的地位。近日业界有消息传出，谷歌有意考虑将Swift作为Android开发的第一语言，而Facebook和Uber也计划在运营中提高Swift的地位。**

虽然这是一则被科技媒体爆出来的新闻，但是让我对Swift报有更强烈的期待。

紧接着一个PR被Swift团队接受了：[https://github.com/apple/swift/pull/1442](https://github.com/apple/swift/pull/1442)

*This adds an Android target for the stdlib. It is also the first example of cross-compiling outside of Darwin: a Linux host machine builds for an Android target.*

目前`Swift`已经支持了Mac和Linux两个平台，虽然`Linux`支持的是`Ubuntu`。

![](https://raw.githubusercontent.com/icepy/_posts/master/img/swift.png)

如果`Swift`是一个江湖，那么：

<!--more-->

**道统**

- [swift-lldb](https://github.com/apple/swift-lldb)
- [swift-clang](https://github.com/apple/swift-clang)
- [swift-llvm](https://github.com/apple/swift-llvm)
- [swift-package-manager](https://github.com/apple/swift-package-manager)

这是江湖中最顶级的道统，天下武功（基于Swift开源的框架或者实现）皆出于此。

**道统管理**

[https://github.com/kylef/swiftenv](https://github.com/kylef/swiftenv)相当于Node.js中的nvm，你可以使用它来管理Swift的版本。当然相比于JavaScript的jsbin，Swift也存在一个Web的运行时，你可以通过它来学习Swift的基础心法：[http://www.runswiftlang.com/](http://www.runswiftlang.com/)。

**道统的公告**

如果你想知道`Swift`下一步的发展计划，你可以访问[https://github.com/apple/swift-evolution](https://github.com/apple/swift-evolution)来了解`Swift`团队的动态，目前的动态信息是Development major version: Swift 3.0，Expected release date: Late 2016。

## 武功用于何处

突然间感觉到Swift与JavaScript的比较，有种相同类似的意义，那么让我们看一看Swift究竟能做些什么。

**开发iOS Mac Apple Watch平台的App**

![](https://raw.githubusercontent.com/icepy/_posts/master/img/apple.png)

这一点上毫无疑问，Apple推出的这一语言目的就是替换Objective-C在iOS，Mac平台上的`地位`（Apple Watch必须使用Swift开发，如果说开源可能谁都没发想到，那一届的WWDC确实很惊喜），有一点需要注意的是，如果你的App需要提交到Apple的商店，那么你必须使用Xcode自带的Swift版本（目前是2.2）。

如果你想学习Swift，我特别的推荐你查看：[https://github.com/ipader/SwiftGuide](https://github.com/ipader/SwiftGuide)，当然官网也是不错的去处。

当然，随着`iOS Mac Apple Watch`平台的武功秘籍，流派的发展各路武功你都可以使用[CocoaPods](https://cocoapods.org/)来进行管理，相当于Node.js之`NPM`。

**Android-虚位以待**

![](https://raw.githubusercontent.com/icepy/_posts/master/img/android.png)

随着科技新闻的曝光和FB工程师的一次PR（开源社区），这个方面绝对有很大的想象空间。如果`Google`决定将`Swift`应用到Andorid平台，这无疑对开发者来说将有大大的好处。

来来来，看一个Swift跑在Android上的`Hello World`：[https://github.com/SwiftAndroid/swift/](https://github.com/SwiftAndroid/swift/)

**服务端**

> Hello，服务端 Swift

如果说安全和性能是Swift最大的优势外，它的简单易学也是它最大的优点。

- [Perfect](https://github.com/PerfectlySoft/Perfect/)
- [Kitura](https://github.com/IBM-Swift/Kitura)
- [Express](https://github.com/crossroadlabs/Express)

`Perfect`是用Swift语言的Web开发和其他REST服务的框架，提供了一套进行服务端和客户端开发的核心工具，尤其是还供了在服务端开发中非常重要的MySQL, PostgreSQL, MondoDB数据库连接器。

`Kitura`是IBM公司开源的一套web开发框架。

`Express`让我想到了Node.js社区的`express`web开发框架，没错你能看见非常熟悉的语法和使用方式。

至于数据库，你想连接哪个都行。

**数据可视化**

数据可视化（哪都有它），比如Web的D3.js，当然Swift也有它对应的实现可用（而且N+1多），我用过的是[https://github.com/danielgindi/Charts](https://github.com/danielgindi/Charts)。

![](https://raw.githubusercontent.com/icepy/_posts/master/img/charts.png)

**AI**

这年头不玩玩人工智能和深度学习都不好意思了，没错Swift也有一个对应的开源实现：[https://github.com/collinhundley/Swift-AI](https://github.com/collinhundley/Swift-AI)。

![](https://raw.githubusercontent.com/icepy/_posts/master/img/ai.png)

------

还有太多太多的领域（硬件，物联网，游戏等等）就不一一例举了，当然它无法进入Web客户端领域（这里绝对是JavaScript的天下。）

## 未来

静静的等待3.0以及它的爆发；
