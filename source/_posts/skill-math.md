title: 奇淫巧技-Math对象
date: 2016-06-24 15:46:34
tags: JavaScript专栏
---

## 什么是Math对象

> Math 是一个内置对象， 为数学常量和数学函数提供了属性和方法，而不是一个函数对象。

Math对象与其它全局对象不同的是，它不是一个构造器。Math对象的所有属性和方法都是静态的。你用到的常数pi可以用`Math.PI`表示，用 x 作参数`Math.sin(x)`调用sin函数。JavaScript中的常数, 是以全精度的实数定义的。

## 奇淫巧技

```JavaScript
Math.abs(-100)

//bad

-5 % 2 === 1  // false

//good

Math.abs(-5 % 2) === 1 //true

```

`abs`方法可以返回一个绝对值，相比之下当字符串转Number是比`parseInt`方法要安全的很多，当然你也可以使用`Number`的方式来处理，如果有一项为非数字的字符串，返回`NaN`，最重要的是它可以将负数解为正数。

在对2取余时`-5`明显是应该等于1的，在这里往往会造成bug，于是`abs`就派上用场了。


```JavaScript
var num = [12,2,3,4,5];
Math.max.apply(null,num) //12

// bad
num.sort(function(x,y){return x < y;});
num[0] // 12

```

`max`方法可以返回0个到多个数值中的最大值，如果有一个数组且需要查找最大数，使用`apply`方法即可以筛选出来。

```JavaScript
var num = [12,3,4,5,6];
Math.min.apply(null,num)
```

`min`方法可以返回0个到多个数值中最小值，使用方式与`max`相同调用`apply`方法将数组当参数传入。

```JavaScript
Math.random() * (10-2)+2
```

`random`方法可以返回0-1之间的随机浮点数，使用它可以返回一个介于2和10之间的随机数。

```JavaScript
console.log(Math.random().toString(16).substring(2));
console.log(Math.random().toString(36).substring(2));
```

`random`还可以用来生成随机码，巧用了`toString`方法。

```JavaScript
Math.floor(Math.random()*(10-2)+2)
```

`floor(x)`方法返回小于或等于数 "x" 的最大整数，使用它我们可以轻易的求去一个介于2和10之间的随机整数。

```JavaScript
Math.ceil(x)
```

`ceil`方法返回一个大于或等于数 "x" 的最小整数。

```JavaScript
var numbers = [20,12,3,4,23,1];
numbers = numbers.sort(function(){ return Math.random() - 0.5});
```

`numbers`可以得到一个`[3, 12, 20, 23, 4, 1]`的乱序。

## 关注我们

**扫二维码** 或搜索 **fed-talk** ，关注我们的微信公众号。

<div align="center">
<img src="https://raw.githubusercontent.com/icepy/_posts/master/img/weixin.jpg" alt=""/><br>
</div>
