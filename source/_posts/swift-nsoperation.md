title: NSOperation实现多线程（Swift）
date: 2015-12-29 11:36:27
tags: Swift
---

在上周，我用GCD去实践练习了一下多线程的使用，这周我将练习一种不同的多线程实现。

为了理解多线程你还需要理解一些概念性的东西，比如什么是串行，什么是并行，什么是临界区，什么是竞态条件。

关于这些概念，推荐一本书[《操作系统:精髓与设计原理》](http://www.amazon.cn/gp/product/B0041859AI?psc=1&ref_=oh_aui_detailpage_o07_s00)，我且当你知道并且了解。

**前言论述**

在Foundation框架中提供了一个叫做`NSThread`的类，从名字上咱们就能看出，这是一个属于跟`线程`操作有关的类，没错。但是使用它来管理多个线程特别的繁琐，需要考虑竞态条件，需要锁等。于是，`NSOperation`和`NSOperationQueue`提供了更高级的封装，来很好的处理多个线程。

**实践练习**

`NSOperation`是一个抽象类，所以不能直接使用它，但是它可以为子类提供有用且线程安全的建立状态，优先级，依赖和取消等操作。

- 继承它来自己实现内部
- 使用`NSBlockOperation`

**注意：Swift中将不存在NSInvocationOperation相关APIs**

`NSOperationQueue`则有些类似`线程池`，我们使用的`NSOperation`都需要添加到`NSOperationQueue`中，用来方便管理这些线程。

**它们应该是一对好兄弟**

NSOperation提供了`ready`，` cancelled`，` executing`， `finished`，这几个状态变化，可以通过`KVO`来通知改变这些状态，一般场景下你可能使用不到这些，除非你自己继承`NSOperation`来实现子类的方式来使用，你才需要管理这些状态。

```Swift
let operation3 = NSBlockOperation(block: {
    _ in
    print("不用NSOperationQueue")
})
operation3.start()
```

**添加依赖**

这个步骤在某些场景下非常有用，比如我先读取本地文件，然后根据文件来发送网络请求，这里可见网络请求是依赖于读取本地文件的。

```Swift
operation2.addDependency(operation1)
```
*注意事项：*

- 避免循环依赖，比如A依赖B，B又依赖A，那么恭喜你，死锁了。

**执行**

如果你使用了`NSOperationQueue`，那么你将不需要手动调用`start`方法，因为队列会帮助我们调用start方法。

**取消**

`NSOperation`允许你取消一个任务，跟GCD一样，如果任务正在执行，且是无法取消的，只能等待任务完成，调用`cancel`方法

**优先级**

通过设置`NSOperation`的`queuePriority`属性来提高某个`NSOperation`的优先级。

```Swift
public enum NSOperationQueuePriority : Int {
    case VeryLow
    case Low
    case Normal
    case High
    case VeryHigh
}
```

**例子**

读取本地文件和网络请求

```Swift
let queue:NSOperationQueue = NSOperationQueue()
//读取文件
let operation1:NSBlockOperation = NSBlockOperation(block: {
    _ in

    let path:String = NSBundle.mainBundle().pathForResource("gulpfile", ofType: "js")!
    let manager:NSFileManager = NSFileManager.defaultManager()
    let isTrue:Bool = manager.fileExistsAtPath(path)
    if isTrue {
        print("文件存在读取 \(path)")
        do{
            let content:String = try NSString(contentsOfURL: NSURL(string: path)!, encoding: NSUTF8StringEncoding) as String
            print("----------读取文件数据---------")
            print("\(content)")
            print("----------读取文件数据---------")
        }catch{
            print("读取错误")
        }
    }
})

//网络请求
let operation2:NSBlockOperation = NSBlockOperation(block: {
    _ in

    let url:String = "https://github.com/icepy"
    let urlObject:NSURL = NSURL(string: url)!
    let request:NSURLRequest = NSURLRequest(URL: urlObject)
    var response:NSURLResponse?
    do{
        let data:NSData = try NSURLConnection.sendSynchronousRequest(request, returningResponse: &response)
        if let HTTPResponse = response as? NSHTTPURLResponse{
            print("状态码：\(HTTPResponse.statusCode)")
            print("============数据===========")
            print("\(data)")
            print("============数据===========")
        }
    }catch{

    }
})
//网络请求在读取文件之前
operation2.addDependency(operation1)

queue.addOperation(operation2)
queue.addOperation(operation1)
//取消网络请求
operation2.cancel()
print("网络请求-同步不会阻塞显示这里")
print("读取文件不会阻塞显示这里")

//NSInvocationOperation 在 Swift不存在相关API

let operation3 = NSBlockOperation(block: {
    _ in
    print("不用NSOperationQueue")
})
operation3.start()
```
