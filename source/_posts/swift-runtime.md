title: Runtime在Swift中的使用
date: 2015-11-27 11:40:32
tags: Swift
---

**源代码基于 Swift 2.1+ Xcode 7.1.1编写**

Runtime让语言具备了灵活的动态特性，关于Runtime的理论知识，可推荐大家阅读：

- [Objective-c Runtime](http://yulingtianxia.com/blog/2014/11/05/objective-c-runtime/)

---

在Swift中也可以使用这样的机制，当然`extension`也为Swift准备了良好的特性可用，然后附上**前人**为我们准备的忠告：*请记住仅在不得已的情况下使用runtime。随便修改基础框架或所使用的三方代码是毁掉你的应用的绝佳方法。请务必要小心哦。*

在Swift中使用Runtime，将不在需要你手动导入`objc/runtime.h`。

**交换两个方法**

在学习Objectice-C Runtime的理论知识时，在初始化类或者加载的时候会调用两个方法`load'和'initialize`。方法交叉过程永远会在 load() 方法中进行，每一个类在加载时只会调用一次 load 方法，但是Swift只会在`initialize`方法中进行。

```Swift
extension UIViewController{
    public override class func initialize(){
        struct Static{
            static var token:dispatch_once_t = 0
        }
        if self != UIViewController.self{
            return
        }
        dispatch_once(&Static.token, {
            _ in
            let viewDidLoad = class_getInstanceMethod(self, Selector("viewDidLoad"))
            let viewDidLoaded = class_getInstanceMethod(self, Selector("viewDidLoaded"))
            method_exchangeImplementations(viewDidLoad,viewDidLoaded)
        })
    }
    func viewDidLoaded(){
        self.viewDidLoaded()
        print("init --- > \(self)")
    }
}
```

**IMP**

从Swift2.0开始指向函数的指针可以直接转换为闭包，只不过我们需要为它添加上@convention标注

```Swift
typealias _IMP = @convention(c)(id:AnyObject,sel:UnsafeMutablePointer<Selector>)->AnyObject
typealias _VIMP = @convention(c)(id:AnyObject,sel:UnsafeMutablePointer<Selector>)->Void

extension UIViewController{
    public override class func initialize(){
        struct Static{
            static var token:dispatch_once_t = 0
        }
        if self != UIViewController.self{
            return
        }

        dispatch_once(&Static.token, {
            _ in
            let viewDidLoad:Method = class_getInstanceMethod(self, Selector("viewDidLoad"))
            let viewDidLoad_VIMP:_VIMP = unsafeBitCast(method_getImplementation(viewDidLoad),_VIMP.self)
            let block:@convention(block)(UnsafeMutablePointer<AnyObject>,UnsafeMutablePointer<Selector>)->Void = {
                (id,sel) in
                viewDidLoad_VIMP(id: id.memory, sel: sel)
                print("viewDidLoad func execu over id ---> \(id.memory)");
            }
            let imp:COpaquePointer = imp_implementationWithBlock(unsafeBitCast(block, AnyObject.self))
            method_setImplementation(viewDidLoad,imp)
        })
    }
}
```
**关联对象**

不过貌似，Swift的extension现在可以直接扩展属性，关联对象就比较用的少了。

```Swift
extension UIViewController{
    private struct Associa{
        static var Name:String = "UIStackView_Name"
    }

    var name:String{
        get{
            return objc_getAssociatedObject(self,&Associa.Name) as! String
        }
        set(newValue){
            objc_setAssociatedObject(self, &Associa.Name, newValue as String?, objc_AssociationPolicy.OBJC_ASSOCIATION_RETAIN_NONATOMIC)
        }
    }
}
```
