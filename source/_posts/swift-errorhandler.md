title: Swift错误处理
date: 2016-01-12 11:33:43
tags: Swift
---

*错误处理是Swift2.0后新推出的特性*

在原来做iOS开发时，通常会使用`NSError`对象来标识一个错误，并且需要传递一个NSError指针到可能发生错误的方法中去。以前，我觉得这个很好用，为什么？因为我可以穿入一个nil来忽略错误，为了省事，但是后来出现的坑，让我更近注重去处理错误了。

在Swift中一个函数或者方法需要使用`throws`关键字标明，此能接收并处理错误的`能力`，一般情况下，会根据`错误类型`来做进一步处理，或者抛给调用者。

每一个错误，都需要遵循`ErrorType`协议，当然Cocoa中已经为我们定义了很多错误，换句话说，我们也可以自己定义自己的错误类型，只要遵循了`ErrorType`协议即可。

## Demo访问

基于Swift 2.1 使用Xcode 7.2编写，访问[Swift Error Handler](https://github.com/icepy/_posts/blob/master/demo/errorHandler/errorHandler/ViewController.swift)

## 场景描述

 自己设计的场景，假设在魔兽世界中，熊猫人可以成为中立阵营（翻墙出岛，正常流程是需要选择一个阵营的），其他两个阵营为联盟与部落。

- 如果是熊猫人（假设翻墙了），那么就抛出一个无分组的错误
- 如果人类选择了部落，那么将抛出一个LGroup错误
- 如果兽人选择了联盟，那么将抛出一个BGroup错误

**定义错误类型**

```Swift
enum GroupError:ErrorType{
    case NoneGroup
    case LGroup
    case BGroup
}
```

**使用throws关键字**

```Swift
func selectorGroup(tag:Int,type:String) throws{
    switch tag{
        case 1:
            if type != "human"{
                throw GroupError.LGroup
            }
            break
        case 2:
            if type != "orc"{
                throw GroupError.BGroup
            }
            break
        default:
            if type == "panda"{
                throw GroupError.NoneGroup
            }
            break
    }
}
```

那么调用者该如何处理呢？Swift提供了`do...catch`和`try`机制，让我们来捕获错误。

```Swift
@IBAction func orcSelector(sender: UIButton) {
    do{
        try selectorGroup(1, type: "orc")
    }catch{
        print("兽人，请不要选择联盟")
    }
}

@IBAction func pandaSelector(sender: UIButton) {
    do{
        try selectorGroup(0, type: "panda")
    }catch{
        print("熊猫人必须选择一个阵营")
    }
}

@IBAction func humanSelector(sender: UIButton) {
    do{
        try selectorGroup(2, type: "human")
    }catch{
        print("人类，请不要选择部落")
    }
}
```

## 在Swift中如何使用NSError

**定义处理函数**

```Swift
func selectorNSError(inout error:NSError){
    error = NSError(domain: "Selector Group Error", code: 500, userInfo: ["message":"选择错误"])
}
```

这里就使用到了`inout`关键字来处理指针， 操作时不需要使用`memory`来操作。额外提一句，如果使用别的方式，操作是需要`memory`的。

```Swift
let num = UnsafeMutablePointer<Int>.alloc(12)
num.memory = 1
```

调用者使用`selectorNSError函数

```Swift
@IBAction func NSErrorHandler(sender: UIButton) {
    var error:NSError = NSError(domain: "", code: 100, userInfo: nil)
    selectorNSError(&error)
    print("\(error)")
}
```
