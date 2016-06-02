title: 使用UIAlertController
date: 2015-11-19 11:44:10
tags: Swift
---

**源代码基于 Swift 2.1+ Xcode 7.1.1编写**

在iOS 8中出现了UIAlertController用来替换以前的UIAlertView与UIActionSheet

**使用它的好处**

- 现在它是一个Controller，意味着可以使用modal或者popover的方式来展示，而且可以从UIViewController的配置属性中获利良多
- UIAlertViewController在配置按钮，文本框时更加的灵活
- 引进了一个新的类UIAlertAction来对按钮的数量，类型，顺序加以控制，比之前更简洁

可以下载[Demo](https://github.com/icepy/withoutMe/tree/master/UIAlertController)来运行，Swift 2.0 Xcode 7.1.1

也可以阅读Apple提供的文档[UIAlertController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UIAlertController_class/index.html)

**初始化alert与actionSheet**

```Swift
lazy var alert:UIAlertController = {
        return UIAlertController(title: "alert", message: "魔兽世界7.0－军团再临资料片", preferredStyle: UIAlertControllerStyle.Alert)
}()
lazy var actionSheet:UIAlertController = {
        return UIAlertController(title: "actionSheet", message: "魔兽世界7.0要塞", preferredStyle: UIAlertControllerStyle.ActionSheet)
}()
```
从风格上来看，使用了一个枚举来描述，它所涵盖的类型有两种：
- Alert 创建一个弹出框
- ActionSheet 创建一个上拉菜单

**创建普通的弹出框**

每一个对应的按钮，现在使用UIAlertAction来创建，对应的动作都成了闭包，从阅读上来说是非常线性的，最后用创建好的UIAlertController顺序添加即可。

```Swift
let closeAlert:UIAlertAction = UIAlertAction(title: "取消", style: UIAlertActionStyle.Cancel, handler: nil)
let okAlert:UIAlertAction = UIAlertAction(title: "下载", style: UIAlertActionStyle.Default, handler: nil)
self.alert.addAction(closeAlert)
self.alert.addAction(okAlert)
```
因为现在成了Controller，所以想要显示可以用`self.presentViewController(self.alert, animated: true, completion: nil);`模态弹出即可。

UIAlertActionStyle提供了三种样式，让我们使用

- Default 标准样式
- Cancel 取消样式
- Destructive 警告样式

**创建带输入框的弹出框**

不得不说，Apple给我们提供了一个很鸡肋的东西，那就是`addTextFieldWithConfigurationHandler`

```Swift
self.alert.addTextFieldWithConfigurationHandler({
     [unowned self] (text:UITextField) in
     text.placeholder = "账户"
})
self.alert.addTextFieldWithConfigurationHandler({
     [unowned self] (text:UITextField) in
     text.placeholder = "邀请码"
     NSNotificationCenter.defaultCenter().addObserver(self, selector:"alertTextFieldDidChange:", name: UITextFieldTextDidChangeNotification, object: text)
})
let okAlert:UIAlertAction = UIAlertAction(title: "升级资料片", style: UIAlertActionStyle.Default, handler: {
     [unowned self] (action:UIAlertAction) in
     NSNotificationCenter.defaultCenter().removeObserver(self, name: UITextFieldTextDidChangeNotification, object: nil)
})
self.alert.addAction(okAlert)
okAlert.enabled = false
```
虽然你可以这样使用，但是，你还是自己用UIViewController创建一个这样的界面比较好。

**创建一个上拉菜单**

从使用上来说，跟之前创建Alert一样，需要使用UIAlertAction来创建每一项菜单。

```Swift
let cancalAction:UIAlertAction = UIAlertAction(title: "取消", style: UIAlertActionStyle.Cancel, handler: nil)
let deleteAction:UIAlertAction = UIAlertAction(title: "删除 ", style: UIAlertActionStyle.Destructive, handler: nil)
let archiveAction:UIAlertAction = UIAlertAction(title: "升级", style: UIAlertActionStyle.Default, handler: nil)
self.actionSheet.addAction(cancalAction)
self.actionSheet.addAction(deleteAction)
self.actionSheet.addAction(archiveAction)
```
