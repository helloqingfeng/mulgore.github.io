title: Swift日期处理
date: 2015-12-5 11:39:20
tags: Swift
---

在软件开发领域，使用频率比较高的我想日期正是其中之一。以前在Objectice-C中可以使用NSDate，NSDateFormatter类来处理日期，换到Swift中也是一样，`唯一需要注意的是iOS中没有毫秒，所有的日期类最小精度只能到秒`。

**NSDate**

NSDate类提供了创建date，比较date以及计算两个date之间间隔的功能，Date对象是不可改变的。

```Swift
let now = NSDate()
print("现在的时间：\(now)")
```

如果你需要与当前日期不同的日期，也可以通过`timeIntervalSinceNow`和`dateByAddingTimeInterval`来创建。

```Swift
let perDay:NSTimeInterval = 24*60*60
let tomorrow = NSDate(timeIntervalSinceNow: perDay)
let _tomorrow = now.dateByAddingTimeInterval(perDay)
print("明天的时间：\(tomorrow)")
let yesterday = NSDate(timeIntervalSinceNow: -perDay)
//增加时间间隔
let _yesterday = now.dateByAddingTimeInterval(-perDay)
print("昨天的时间：\(yesterday)")
```

如果你需要比较两个日，最常用的是使用`timeIntervalSinceDate`方法。

```Swift
//比较时间，如果两个时间间隔小于一分钟，可认为在同一天
if tomorrow.timeIntervalSinceDate(yesterday) < 60 {
      //相等
}else{
     //不相等
}
```

如果你想精度更高的进行比较，那么可以使用：
- `tomorrow.isEqualToDate(<#T##otherDate: NSDate##NSDate#>)`
- `tomorrow.compare(<#T##other: NSDate##NSDate#>)`
- `tomorrow.laterDate(<#T##anotherDate: NSDate##NSDate#>)`
- `tomorrow.earlierDate(<#T##anotherDate: NSDate##NSDate#>)`

**NSDateFormatter**

NSDate本身并不能输出或者格式化，需要借助NSDateFormatter来进行处理，NSDateFormatter的初始化是一个比较消耗的创建，所以一般都要懒加载来处理它。

```Swift
lazy var dateFormatter = {
    return NSDateFormatter()
}()
```
从程序的一般逻辑来看，formatter可以看做是（输入｜输出）的关系，所以NSDateFormatter中供给的方法大部分都是设置属性，获取NSDate对象。

```Swift
let location = NSLocale(localeIdentifier: "zh-CN")
let timeString = "20110826134106"
self.dateFormatter.locale = location
self.dateFormatter.dateFormat = "yyyyMMddHHmmss"
let date:NSDate = self.dateFormatter.dateFromString(timeString)!
print(date)
```
NSLocale可以设置本地化时间，然后将格式化字符串设置给dateFormat，就可以使用了。

**NSCalendar & NSDateComponents**

NSCalendar定义了不同的日历，包括佛教历，格里高利历等（这些都与系统提供的本地化设置相关）而
NSDateComponents则是定义了一个日期对象的组件，包括`年，月，日，小时，分钟，秒`等等，另外NSDateComponents也可以去获取时间（比如计算跟某某时间相差多少时间）

初始化NSCalendar，需要注意的是iOS8.0之后，标识开始要使用NSCalendarIdentifier（xxxx）了

```Swift
let calendar = NSCalendar(calendarIdentifier: NSCalendarIdentifierGregorian)
```

初始化NSDateComponents

```Swift
let components = NSDateComponents()
components.year = 2105
components.month = 12
components.day = 5
```
获取某个时间的相差值，需要注意的是，iOS8.0开始要使用NSCalendarUnitYear了，原来的NSYearCalendarUnit将要废弃。

```Swift
let calendar = NSCalendar(calendarIdentifier: NSCalendarIdentifierGregorian)
let unitF:NSCalendarUnit = [NSCalendarUnit.Year,NSCalendarUnit.Month,NSCalendarUnit.Day,NSCalendarUnit.Hour,NSCalendarUnit.Minute,NSCalendarUnit.Second]
let dateComponents:NSDateComponents? = calendar?.components(unitF, fromDate: now, toDate: yesterday, options: .MatchNextTimePreservingSmallerUnits)
print("年：\(dateComponents?.year)")
print("月：\(dateComponents?.month)")
print("日：\(dateComponents?.day)")
print("小时：\(dateComponents?.hour)")
print("分钟：\(dateComponents?.minute)")
print("秒：\(dateComponents?.second)")
```
