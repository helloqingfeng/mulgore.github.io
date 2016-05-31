title: 引用计数的关系理解
date: 2015-11-9 11:46:49
tags: Swift
---

**源代码基于 Swift 2.1+ Xcode 7.1.1编写**

**Swift ARC**

`weak `  `unowned`的区别，以及如何解除强循环引用

`weak`用弱引用来描述，它在此中类似于一个可选?。

`unowned`用无主引用来描述，它在此中类似于一个解包!。

**闭包**

闭包中为什么使用unowned，那是因为引用的self是一个必须有值的情况，如果你的引用某些情况下是没有值，那么才可以使用weak。

```swift
class React {
    var doc:String
    var asHTML:(html:String)->String? = {
        (html:String) -> String? in
        return nil
    }

    //无参数

    lazy var parseHTML:Void -> String = {
        [unowned self]() -> String in
        if let dom:String = self.doc{
            return "<p>\(dom)</p>"
        }else{
            return "<h1>no Name DOM </h1>"
        }
    }

    //有参数

    lazy var stringfyHTML:(document:String)->String = {
        [unowned self](document:String) -> String in

        return "\(document) is DOM"
    }

    init(doc:String){
        self.doc = doc
    }

    func way(){
        self.asHTML(html: self.doc)
    }

    deinit{
        print("释放自动引用计数")
    }
}

var react:React?
react = React(doc: "<em>icepy</em>")
react?.asHTML = {
    (html:String) -> String? in
    print(html)
    return nil
}
react?.parseHTML()
react?.stringfyHTML(document:"icepy")
react?.way()
react = nil
```

**弱引用 weak**

`弱引用不会对其引用的实例保持强引用，因而不会阻止 ARC 销毁被引用的实例。但是弱引用可以没有值，所以必须将每一个弱引用声明为可选类型`

```swift
//用菇凉来描述

class Girls {
    var tag:GirlType?
    init(){

    }
    func say(){
        print("girl \'s tag is \(self.tag?.name)")
    }
    deinit{
        print("Girls 释放")
    }
}

class GirlType{
    weak var what:Girls?
    var name:String
    init(name:String){
        self.name = name
    }
    deinit{
        print("GirlType 释放")
    }
}

var liuyueying:Girls? = Girls()
var type:GirlType? = GirlType(name: "女神")
liuyueying?.tag = type
type!.what = liuyueying
liuyueying?.say()
liuyueying = nil
type = nil
```

**无主引用 unowned**

`和弱引用类似，无主引用不会牢牢保持住引用的实例。和弱引用不同的是，无主引用是永远有值的。`

 ```swift
//阵营中不一定有兽人

class Camp {
    var description:String{
        return "Camp"
    }
    var tagName:String
    var race:Races?
    init(name:String){
        self.tagName = name
    }

    deinit{
        print("\(self.description) 释放")
    }
}

//兽人必然有一个阵营

class Races {
    unowned let belong:Camp
    var description:String{
        return "Races"
    }
    var raceName:String
    init(name:String,camp:Camp){
        self.raceName = name
        self.belong = camp
    }
    deinit{
        print("\(self.description) 释放")
    }
}

var tribe:Camp? = Camp(name: "部落")
tribe!.race = Races(name: "兽人",camp: tribe!)
tribe = nil
```
