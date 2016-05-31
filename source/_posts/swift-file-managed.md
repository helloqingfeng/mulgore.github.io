title: Swift文件操作
date: 2016-01-20 11:32:31
tags: Swift
---

一个应用程序，总会有些文件需要操作。

在iOS中文件操作主要分为两个部分，获取地址，然后操作文件：

- 如果你的文件资源在bundle内，那么需要获取bundle。
- 如果你的文件资源在沙盒内，那么需要获取沙盒地址。
- NSFileManager操作或者流操作。

**bundle**

bundle是一个目录，其中包含了很多资源，有图片，编译好的源代码等等（也可以自己拖动任意文件到Xcode中，它就在此bundle之下。），cocoa提供了NSBundle类来帮助我们获取bundle下的资源。

```Swift
let bundle = NSBundle.mainBundle()
let imagePath = bundle.pathForResource("github","png")
```

一般情况下，我们是通过`mainBundle`来获取bundle对象，当然你也可以自己指定bundle对象。

**沙盒机制**

iOS中的应用程序，每一个都有其自己访问的`作用域`，它有一些非常显著的特点：

- 每个应用程序都有自己的存储空间
- 每个应用程序都只能访问自己存储空间内的资源
- 每个应用程序请求数据时都需要有自己的认证权限

默认情况下Apple给我们提供了三个有意义的目录，它们分别是：

- Documents
- Library
- tmp

`Documents`，Apple建议将程序中创建或者程序中浏览的数据存储在此，iTunes会备份和恢复时会包含此目录。`Library`用来存储默认的数据或者状态，tmp用来存储临时文件（iOS会随时清除）。

一般情况下，我们都使用`NSSearchPathForDirectoriesInDomains`来获取沙盒路径。

```Swift
private let cacheDirPath:NSString = NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory.CachesDirectory, NSSearchPathDomainMask.UserDomainMask, true)[0] as NSString
private let docDirPath:NSString = NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory.DocumentDirectory, NSSearchPathDomainMask.UserDomainMask, true)[0] as NSString
```

**NSFileManager**

NSFileManager提供了一系列的操作，包括有创建，删除，移动，查询等等。获取此对象，可以使用一个静态方法来获取单例，或者实例化一个NSFileManager对象。

```Swift
lazy var manager:NSFileManager = {
    return NSFileManager.defaultManager()
}()
```

判断文件是否存在：

```Swift
self.manager.fileExistsAtPath(rmPath)
```

创建一个文件：

```Swift
self.manager.createFileAtPath(file.path!,contents: data, attributes:nil)
```

删除一个已经存在的文件：

```Swift
self.manager.removeItemAtPath(rmPath)
```

移动一个文件：

```Swift
self.valiantCenter.manager.moveItemAtPath(fromPath, toPath: self.info.saveZipPath)
```

有一点在Swift2.0之后需要注意，现在的错误处理已经变成了do...catch机制，所以不管是删除还是移动，都需要try的配合。当然，关于NSFileManager的API肯定不是只有这么点，它还包括了搜索文件，深递归目录下所有的文件等等，详细的API信息，需要你自己慢慢去查看了。

```Swift
do{
    try self.valiantCenter.manager.moveItemAtPath(fromPath, toPath: self.info.saveZipPath)
    self.finishDownloadZipTask()
}catch{
   self.addContext(ValiantError(reason: "下载成功：\(self.id) 移动临时数据到保存目录错误：\(self.info.saveZipPath)"))
}
```

**NSInputStream|NSOutputStream**

关于流操作大部分还是会应用在网络方面，比如要将一个很大的文件传送给服务器，那么NSInputStream这时候就是很好的选择，这样会节省很多内存。使用它来单纯的操作文件会比较少，那么操作文件，我们主要使用cocoa提供的`NSInputStream`类来帮助我们更简单的操作流。

一般情况下从`NSInputStream`读入数据需要下列几个步骤：

- 创建一个`NSInputStream`实例
- 通过流对象的Delegate处理一些`状况事件`

```Swift
let stream = NSInputStream.inputStreamWithFileAtPath("file.txt")
stream.read(_ buffer: UnsafeMutablePointer<UInt8>,
maxLength len: Int)
```
