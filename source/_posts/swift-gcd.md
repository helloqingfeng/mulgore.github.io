title: GCD（Swift）
date: 2015-12-21 11:37:35
tags: Swift
---

GCD是基于C API开发的一套库，苹果公司将它应用在（iOS或者OS X）上，为我们极大的方便来处理并发代码。

`能为我们带来什么？`

- GCD 能通过推迟昂贵计算任务并在后台运行它们来改善你的应用的响应性能。
- GCD 提供一个易于使用的并发模型而不仅仅只是锁和线程，以帮助我们避开并发陷阱。
- GCD 具有在常见模式（例如单例）上用更高性能的原语优化你的代码的潜在能力。

为了理解GCD你还需要理解一些概念性的东西，比如什么是串行，什么是并行，什么是临界区，什么是竞态条件。

关于这些概念，推荐一本书[《操作系统:精髓与设计原理》](http://www.amazon.cn/gp/product/B0041859AI?psc=1&ref_=oh_aui_detailpage_o07_s00)，我且当你知道并且了解。

Apple帮助我们定义好了两个常量来创建`串行`，`并行`的GCD任务：

- DISPATCH_QUEUE_SERIAL  串行
- DISPATCH_QUEUE_CONCURRENT 并行

利用`dispatch_..._...`可以轻松的创建这些任务，如图：

![GCD创建的多任务](https://raw.githubusercontent.com/icepy/_posts/master/img/GCDdebug.png)

Demo例子基于Swift 2.1 Xcode 7.2 编写，可在此处[查看](https://github.com/icepy/_posts/blob/master/demo/GCD/GCD/ViewController.swift)

**将一个block提交到主线程中执行**

通过`dispatch_get_main_queue`来获取主线程队列。

```Swift
dispatch_async(dispatch_get_main_queue()){
    [unowned self] _ in
    let myPhoneMain:String = "main queue iPhone";
    print("main queue is \(myPhoneMain)")
}
```

**将一个block提交到串行任务队列中**

```Swift
let window:dispatch_queue_t = dispatch_queue_create("com.wen.window", DISPATCH_QUEUE_SERIAL)
dispatch_async(window){
    [unowned self] _ in
    let myWindow:String = "window 7"
    print("我的电脑操作系统：\(myWindow)")
}
```

**将一个block提交到并行任务队列中**

```Swift
let phone:dispatch_queue_t = dispatch_queue_create("com.wen.iphone", DISPATCH_QUEUE_CONCURRENT)
dispatch_async(phone){
    [unowned self] _ in

    let myPhone:String = "iPhone 6 Plus"
    print("my Phone is \(myPhone)")

    //切换到主线程
    dispatch_async(dispatch_get_main_queue()){
        [unowned self] _ in
        let myPhoneMain:String = "main queue iPhone";
        print("main queue is \(myPhoneMain)")
    }
}
```

**将一个block提交到延迟任务队列中**

`dispatch_after`可以帮助我们延时提交`block`到任务队列，当我们创建`dispatch_time_t`变量时稍微注意一下即可：

- NSEC_PER_SEC 一秒有多少纳秒
- USEC_PER_SEC 一秒有多少毫秒
- NSEC_PER_USEC 一毫秒有多少纳秒

```Swift
let exeTime:dispatch_time_t = dispatch_time(DISPATCH_TIME_NOW, 10)
let mac:dispatch_queue_t = dispatch_queue_create("com.wen.mac", DISPATCH_QUEUE_CONCURRENT)
dispatch_after(exeTime, mac){
    [unowned self] _ in

    let myMac:String = "MacBook Pro"
    print("my mac is \(myMac)")
}
```

**将一个block提交到只执行一次的任务中**

这个非常适合做`单例`，唯一需要注意的地方是，`dispatch_once_t`最好是全局或者static变量，因为调试的时候不会出现稀奇古怪的问题。

```Swift
struct oneToken{
    static var onePred:String? = nil
    static var toKen:dispatch_once_t = 0;
}
dispatch_once(&oneToken.toKen){
    [unowned self] _ in
    oneToken.onePred = "icepy"
}
print("once pred is \(oneToken.onePred)")
```

**将block挂起或者恢复**

虽然此处可以挂起，但是并不能保证可以立即停止队列中正在运行的block，所以没法更精准的控制block。

```Swift
let icepy:dispatch_queue_t = dispatch_queue_create("com.wen.suspend", DISPATCH_QUEUE_SERIAL)
dispatch_async(icepy){
    NSThread.sleepForTimeInterval(8)
    let callback:String = "Swift ---"
    print("延迟执行第一个提交:\(callback)")
}
dispatch_async(icepy){
    NSThread.sleepForTimeInterval(8)
    let callback:String = "Objective-C ---"
    print("延迟执行第二个提交:\(callback)")
}


print("延迟1秒")
NSThread.sleepForTimeInterval(1)
print("--- 挂起")
dispatch_suspend(icepy)

print("延迟10秒")
NSThread.sleepForTimeInterval(8)
print("--- 恢复")
dispatch_resume(icepy)
```

**向一个队列添加多个block**

`dispatch_apply`有一个毛病，就是会阻塞外部线程，所以如果要使用还需要注意。

```Swift
let more:dispatch_queue_t = dispatch_queue_create("com.wen.more", DISPATCH_QUEUE_SERIAL)
dispatch_apply(3, more){
    [unowned self] (i:Int) -> Void in

    print("apply loop \(i)")
}
print("after apply")
```

**dispatch_group**

创建一个`dispatch_group`分为三步：

- 创建`dispatch_group_t`
- 创建`dispatch_queue_t`并使用`dispatch_group_async`将`dispatch_queue_t`添加到group中
- 添加结束任务，比如`dispatch_group_notify`

```Swift
let group:dispatch_group_t = dispatch_group_create()
let thread1:dispatch_queue_t = dispatch_queue_create("com.wen.thread1", DISPATCH_QUEUE_CONCURRENT)
let thread2:dispatch_queue_t = dispatch_queue_create("com.wen.thread2", DISPATCH_QUEUE_CONCURRENT)
let notify:dispatch_queue_t = dispatch_queue_create("com.wen.notify", DISPATCH_QUEUE_SERIAL)
dispatch_group_async(group, thread1){
    _ in
    let myBook:String = "JavaScript"
    print("my book is \(myBook)")
}

dispatch_group_async(group, thread2){
    _ in
    let myBook:String = "Swift"
    print("my book is \(myBook)")
}
dispatch_group_notify(group, notify){
    _ in
    let myPro:String = "web developer and iOS developer"
    print("my pro is \(myPro)")
}
```

**避免死锁**

说到死锁，让我想起来一个`笑话`，心里嘿嘿笑一笑就好。如果使用同步的方法`dispatch_sync(<#T##queue: dispatch_queue_t##dispatch_queue_t#>, <#T##block: dispatch_block_t##dispatch_block_t##() -> Void#>)`稍微不注意，就要恭喜你中奖了：

```Swift
func send(){
    dispatch_sync(dispatch_get_main_queue()){
        _ in
        //恭喜
    }
}

dispatch_sync(dispatch_get_main_queue()){

    _ in
    send()
}
```
