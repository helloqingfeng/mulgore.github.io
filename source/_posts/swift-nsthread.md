title: NSThread（Swift）
date: 2016-01-5 11:35:30
tags: Swift
---

`Thread`是iOS中处理线程最轻量的一种方式，而且非常直观的在操作线程，唯一的缺陷是需要自己去管理线程的生命周期，协调多个线程对同一个数据的操作。

**创建NSThread**

```Swift

NSThread.detachNewThreadSelector("detacThread", toTarget: self, withObject: nil)

self.thread = NSThread(target: self, selector: "oneThread", object: nil)
self.thread!.threadPriority = 1.0
self.thread!.start()

```

**使用NSThread下载图片**

这里我使用`storyboard`拖了一个`UIButton`和`UIImageView`出来，并且连接到`UIViewController`中：

```Swift
@IBOutlet weak var imageView: UIImageView!

@IBAction func downloadImages(sender: UIButton) {

      //业务逻辑
}
```

开始初始化`NSThread`

```Swift
if self.clickLock == nil{
     self.clickLock = true
     self.thread = NSThread(target: self, selector: "oneThread", object: nil)
     self.thread!.threadPriority = 1.0
     self.thread!.start()
}
```

调用的是当前`oneThread`方法

```Swift
func oneThread(){
    //需要管理线程的生命周期、同步、加锁问题，这会导致一定的性能开销
    let url:String = "http://content.battlenet.com.cn/wow/media/screenshots/selfie/selfie001-large.jpg"
    let urlObject:NSURL = NSURL(string: url)!
    let request:NSURLRequest = NSURLRequest(URL: urlObject)
    var response:NSURLResponse?
    do{
        let data:NSData = try NSURLConnection.sendSynchronousRequest(request, returningResponse: &response)
        if let HTTPResponse = response as? NSHTTPURLResponse{
            print("状态码：\(HTTPResponse.statusCode)")
            print("============数据===========")
            print("============数据===========")
            self.clickLock = nil
            let current:NSThread = NSThread.currentThread()
            let main:NSThread = NSThread.mainThread()
            self.performSelectorOnMainThread("updateUI:", withObject: data, waitUntilDone: true)
        }
    }catch{

    }
}
```

**如何加锁，操作同一个数据**

`NSThread`对于同步是需要自己去处理了，为了操作数据达到同步的目的，我们需要去加上锁，iOS中存在两种锁`NSCondition`和`NSLock`，这里我使用的是`NSLock`来处理。

```Swift
while true{
    self.theLock?.lock()
    if self.count! >= 0 {
        NSThread.sleepForTimeInterval(0.08)
        self.index = 100 - self.count!
        print("数目-----> \(self.count) 出售 ----> \(self.index) 线程名 ----> \(NSThread.currentThread().name)")
        self.count!--
    }else{
        break
    }
    self.theLock?.unlock()
}
```
