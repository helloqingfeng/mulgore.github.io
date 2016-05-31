title: 在Swift中如何使用指针并操作指针
date: 2015-11-14 11:45:27
tags: Swift
---

**源代码基于 Swift 2.1+ Xcode 7.1.1编写**

如果你还不了解指针，请先阅读[C语言指针5分钟教程](http://blog.jobbole.com/25409/)。

在Swift中指针用两个特殊的类型来描述，UnsafePointer<T>和UnsafeMutablePointer<T>，遵循Cocoa的原则，可以看出`不可变`与`可变`的不同，当我们创建指针之后，就可以通过memory来操作指针了。另外，Swift中UnsafeBufferPointer<T>来描述一组连续的数据指针以及非完整结构不透明指针COpaquePointer

**在函数中传递指针**

在函数中传递指针有两种方式，一：使用`inout`关键字。二：使用Swift准备的指针类型。区别，我用注释写在具体的例子中。

且先看看`inout`关键字，如何操作

```Swift
var num:Int = 10
func some(inout numb:Int){
    //如果使用inout关键字，在函数体内部不需要处理指针类型，可直接操作
    numb += 1
    print("numb --> \(numb)")
}
some(&num)
print("num ---> \(num)")
```

使用Swift准备的指针类型

```Swift
var num:Int = 10
func some(numb:UnsafeMutablePointer<Int>){
    //如果使用Swift提供的类型，那么需要使用memory来进行操作
    numb.memory += 1
    print("numb --> \(numb.memory)")
}
some(&num)
print("num ---> \(num)")
```

需要注意的是，Swift中地址符&是不能直接使用的，只能是函数传递时才可用。

**如何直接操作**

虽然无法像OC或者C中那样直接通过地址符&来操作指针，但是Swift也提供了辅助的方法间接的来帮助我们来操作指针。

```Swift
var i:Int = 10
i = withUnsafeMutablePointer(&i, {
   (p:UnsafeMutablePointer<Int>) -> Int in
       p.memory += 20
       return p.memory
})
print("i value ---> \(i)")
```

**如何使用指向数组的指针**

如果只是函数传递，不可变直接传递，可变使用&符即可，如果想直接操作，那么还是需要UnsafeBufferPointer<T>来辅助完成。

```Swift
var array:[Int] = [2,1,3,4]
var arrayPtr:UnsafeMutableBufferPointer<Int> = UnsafeMutableBufferPointer<Int>(start: &array, count: array.count)
var baseArrayPtr:UnsafeMutablePointer<Int> = arrayPtr.baseAddress as UnsafeMutablePointer<Int>
var nextPtr:UnsafeMutablePointer<Int> = baseArrayPtr.successor()
var threPtr:UnsafeMutablePointer<Int> = nextPtr.successor()

print("第一个元素  \(baseArrayPtr.memory)")
print("第二个元素 \(nextPtr.memory)")
print("第三个元素  \(threPtr.memory)")
```

**如何通过指针强制转换类型**

这个操作比较危险，除非你明确预期知道类型，不然编译器是无法知道的，也就造成了非常大的不确定性。

```Swift
let arr:NSArray = NSArray(object: "icepy")
let str:NSString = unsafeBitCast(arr[0],NSString.self)

print("str --- > \(str.stringByAppendingPathComponent("app"))")
```

**创建一个指针**

```Swift
//创建可变指针
var i:UnsafeMutablePointer<String> = UnsafeMutablePointer<String>.alloc(10)
i.initialize("icepy")
```

**内存管理**

还有一点要注意的是，如果是属于自己手动创建的指针，Swift是不负责管理内存的，需要手动的销毁与释放。

一个UnsafeMutablePointer内存一般有三个状态：

* 内存没有被分配，null指针
* 内存进行了分配，且值还未初始化
* 内存进行了分配，且值已经初始化

```Swift
var i:UnsafeMutablePointer<String> = UnsafeMutablePointer<String>.alloc(10)
i.initialize("icepy")        
print("i的内存地址 \(i)")
print("i的memory \(i.memory)")        
i.destroy() //销毁指针指向的对象
i.dealloc(10) //销毁指针申请的内存
i = nil
```
