title: Swift处理网络请求
date: 2015-12-14 11:38:29
tags: Swift
---

iOS 7.0之后，推荐使用NSURLSession，所以以下网络请求的处理皆是使用NSURLSession来处理。

**如果是Xcode7.0之后，想访问HTTP，需要在info.plist中设置App Transport Security Settings下的Allow Arbitrary Loads为YES**

**如果你想使用细粒度的NSURLSession，那么你就需要与Delegate进行交互了，例子皆是使用闭包的方式实现**

NSURLSession提供如下功能：

- 通过URL将数据下载到内存
- 通过URL将数据下载到文件系统
- 将数据上传到指定的URL
- 在后台完成上述功能

NSURLSession的工作模式：

- 一般模式default，可以使用缓存的Cache，Cookie等
- 不使用缓存模式ephemeral，不使用缓存的Cache，Cookie，权限验证等
- 后台模式background，在后台完成上传下载

**如果需要使用POST，那么你就需要使用NSMutableURLRequest，来设置HTTPMethod或HTTPBody**

**如果使用后台工作模式，那么你将需要与ApplicationDelegate进行交互。**

如果你有Node.js的使用经验，我已经为你准备好了一个简单的服务器，可访问[https://github.com/icepy/_posts/blob/master/demo/NSURLSession/service.js](https://github.com/icepy/_posts/blob/master/demo/NSURLSession/service.js)

详细的Demo例子，可访问[https://github.com/icepy/_posts/tree/master/demo/NSURLSession/NSURLSession](https://github.com/icepy/_posts/tree/master/demo/NSURLSession/NSURLSession)

## NSURLSession交互图

![NSURLSession交互图](https://raw.githubusercontent.com/icepy/_posts/master/img/NSURLSession.jpg)

## 例子

**普通的GET请求：**

```Swift
let requets:NSURLRequest = NSURLRequest(URL: self.url)
let configuration:NSURLSessionConfiguration = NSURLSessionConfiguration.defaultSessionConfiguration()
let session:NSURLSession = NSURLSession(configuration: configuration)
let task:NSURLSessionDataTask = session.dataTaskWithRequest(requets, completionHandler: {
    [unowned self](data:NSData?,response:NSURLResponse?,error:NSError?)->Void in
       if error == nil{
         do{
             let responseData:NSDictionary = try NSJSONSerialization.JSONObjectWithData(data!, options: NSJSONReadingOptions.AllowFragments) as! NSDictionary
             print("普通GET请求 --- > \(responseData)")
        }catch{

        }
      }
})
task.resume()
```

**设置头以及带参数的GET请求：**

```Swift
let url:NSURL = NSURL(string: "http://127.0.0.1:8900/add?id=1&session=icepyquery")!
let request:NSMutableURLRequest = NSMutableURLRequest(URL:url)
request.addValue("ICEPY", forHTTPHeaderField: "Session-Control-Key")
let configuration:NSURLSessionConfiguration = NSURLSessionConfiguration.defaultSessionConfiguration()
let session:NSURLSession = NSURLSession(configuration: configuration)
let task:NSURLSessionDataTask = session.dataTaskWithRequest(request, completionHandler: {
    [unowned self](data:NSData?,response:NSURLResponse?,error:NSError?)->Void in
      if error == nil{
         do{
            let responseData:NSDictionary = try NSJSONSerialization.JSONObjectWithData(data!, options: NSJSONReadingOptions.AllowFragments) as! NSDictionary
            print("普通带头与参数的GET请求 --- > \(responseData)")
         }catch{

         }
     }
})
task.resume()
```

**设置头以及带参数的POST请求：**

如果不想设置头可以不要使用`addValue方法`，参数必须设置在`HTTPBody`中。

```Swift
let request:NSMutableURLRequest = NSMutableURLRequest(URL: self.url)
request.HTTPMethod = "POST"
do{
    let data:NSData = try NSJSONSerialization.dataWithJSONObject(NSDictionary(object: "icepy", forKey: "name"), options: NSJSONWritingOptions.PrettyPrinted)
    request.HTTPBody = data
}catch{

}
request.addValue("wen", forHTTPHeaderField: "Session-Control-Key")
let configuration:NSURLSessionConfiguration = NSURLSessionConfiguration.defaultSessionConfiguration()
let session:NSURLSession = NSURLSession(configuration: configuration)
let task:NSURLSessionDataTask = session.dataTaskWithRequest(request, completionHandler: {
    [unowned self](data:NSData?,response:NSURLResponse?,error:NSError?) -> Void in
    if error == nil{
        do{
            let responseData:NSDictionary = try NSJSONSerialization.JSONObjectWithData(data!, options: NSJSONReadingOptions.AllowFragments) as! NSDictionary
            print("普通空的POST请求 --- > \(responseData)")
        }catch{

        }
    }
})
task.resume()
```

**下载：**

下载的处理，稍微有些不同，NSURLSession的DownloadTask会将下载的内容下载在临时temp目录，下载完成之后需要将内容从临时目录移动到你的保存目录，在移动之前还需要去判断一下是否已经存在，如果已经存在需要先删除。

```Swift
let url:NSURL = NSURL(string: "http://content.battlenet.com.cn/wow/media/screenshots/screenshot-of-the-day/warlords-of-draenor/warlords-of-draenor-ss0420-large.jpg")!
let request:NSURLRequest = NSURLRequest(URL: url)
let configuration:NSURLSessionConfiguration = NSURLSessionConfiguration.defaultSessionConfiguration()
let session:NSURLSession = NSURLSession(configuration: configuration)
let task = session.downloadTaskWithRequest(request, completionHandler: {
    [unowned self](location:NSURL?,response:NSURLResponse?,error:NSError?) -> Void in
    if error == nil{
        if let fromPath = location!.path{
            let file:NSString = docDirPath.stringByAppendingPathComponent("wow.png")
            if self.removeFile(){
                do{
                    try self.manager.moveItemAtPath(fromPath, toPath: file as String)
                    dispatch_async(dispatch_get_main_queue(), {
                        [unowned self] _ in
                        self.imageView.image = UIImage(named: file as String)
                    })
                }catch{
                    print("移动临时数据到保存目录出错")
                }
            }
        }
    }
})
task.resume()
```

删除的方法：

```Swift
private func removeFile() -> Bool{
    let file:NSString = docDirPath.stringByAppendingPathComponent("wow.png")
    if self.manager.fileExistsAtPath(file as String){
        do{
            try self.manager.removeItemAtPath(file as String)
        }catch{
            return false
        }
    }
    return true
}
```

**上传：**

上传可以使用两种方式来实现

- NSURLSessionUploadTask
- NSURLSessionDataTask

`NSURLSessionUploadTask`上传成功之后会下载返回结果，反之`NSURLSessionDataTask`不会。

POST表单上传：

上传还可以利用POST构造一个HTTP表单来完成，需要注意的是使用正确的上传表单头，以及正确的构造主体。

        //multipart/form-data  上传所使用的Content-Type
        //image/jpg  上传类型

```Swift
let url:NSURL = NSURL(string: "http://pitayaswift.sinaapp.com/pitaya.php")!
//模拟表单提交
let request:NSMutableURLRequest = NSMutableURLRequest(URL:url)
request.HTTPMethod = "POST"
request.addValue("Content-Type", forHTTPHeaderField: "multipart/form-data; boundary=\(boundary)")
request.HTTPBody = self.setRequestFile(request)
let configuration:NSURLSessionConfiguration = NSURLSessionConfiguration.defaultSessionConfiguration()
let session:NSURLSession = NSURLSession(configuration: configuration)
let task:NSURLSessionDataTask = session.dataTaskWithRequest(request, completionHandler: {
    [unowned self](data:NSData?,response:NSURLResponse?,error:NSError?) -> Void in
        print(error)
})
task.resume()
```

构造主体方法：

```Swift
private func setRequestFile(request:NSMutableURLRequest)-> NSData{
    var header = request.allHTTPHeaderFields
    let body = NSMutableData()
    let fileType:STFType = STFType(name: "logo", type: "jpg")
    let codeName:String = "file"
    let fileUrl:String? =  NSBundle.mainBundle().pathForResource(fileType.fileName, ofType:fileType.fileType)
    if let contentType:AnyObject = header!["Content-Type"]{
        print("set Content-Type --- \(contentType)")
    }else{
        print("set Content-Type --- empty")
    }
    let STFileParams:[STFile] = [STFile(name: codeName, url: fileUrl,data:nil)]
    for file:STFile in STFileParams{
        if file.fileData == nil{
            body.appendData("--\(boundary)\r\n".STNSData)
            let _fileURL = NSURL(fileURLWithPath: file.fileURL!)
            body.appendData("Content-Disposition: form-data; name=\"\(file.fileName)\"; filename=\"\(_fileURL.lastPathComponent)\"\r\n\r\n".STNSData)
            if let fileData = NSData(contentsOfFile: file.fileURL!){
                body.appendData(fileData)
                body.appendData("\r\n".STNSData)
            }
        }else{
            body.appendData(file.fileData!)
        }
    }
    body.appendData("--\(boundary)--\r\n".STNSData)
    return body
}
```

## 留给大家一个小题目

如何使用`NSURLSessionUploadTask`来实现上传
