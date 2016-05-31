title: lazy修饰符
date: 2015-11-22 11:43:09
tags: Swift
---

**源代码基于 Swift 2.1+ Xcode 7.1.1编写**

大家都知道延迟加载或者说延迟初始化是很常用的一个优化方式，因为在构建和生成新的对象时，在运行时分配内存的时候会耗费不少时间，如果初始化的一些对象特别复杂，有大量的计算动作，那么这显然不可忽略了。

为了达到这样的目的，在Objective-C中，我们可以如此进行延迟一个对象的初始化：

```Objective-C
//WebViewBridge.h
//伪代码
@property(nonatomic, strong) WebViewJavascriptBridge *bridge;

//WebViewBridge.m
-(WebViewJavascriptBridge *)bridge
{
    if (!_bridge) {
        _bridge = [WebViewJavascriptBridge bridgeForWebView:self.webview webViewDelegate:self handler:^(id data, WVJBResponseCallback responseCallback) {
            responseCallback(@"启动 webview bridge");
        }];
    }
    return _bridge;
}
```
在初始化WebViewBridge对象时，_bridge是个nil，当我首次调用时bridge属性会调用它的getter方法，从逻辑上就可以看出，它会检查_bridge，如果还未初始化，就进行初始化，如果已经初始化，就返回_bridge。

而在Swift中这一逻辑判断减少成了一个修饰符`lazy`，没错非常简单的完成了延迟初始化。

```swift
lazy var bridge:WebViewJavascriptBridge = {
    return WebViewJavascriptBridge(forWebView: self.webview, webViewDelegate: self, handler: { (data, WVJBResponseCallback) -> Void in

    })
}()
```
当然，在Swift中使用lazy还有一些限制，如下：

- 只能声明变量，不能使用let
- 需要指定变量的类型
- 显示的执行的这个闭包

其实，还有一个非常有意思的特性，标准库中，提供了一组lazy的方法，可以去延迟，比如数组的map，filter等操作

```Swift
let data = 1...3  
let result = data.lazy.map {  
    (i: Int) -> Int in
    print("处理 \(i)")
    return i * 2
}
for i in result {  
    print("操作后结果为 \(i)")
}
print("操作结束")  
```
