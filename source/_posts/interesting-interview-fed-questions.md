title: 你不得不看的-有趣的前端面试
date: 2016-06-13 16:40:34
tags: JavaScript
banner: http://o80ub63i5.bkt.clouddn.com/image/rubber-duck-1401225_640.jpg
---

### 变量提升

这个知识点是在`JavaScript`中`function`和`var`会被提升，所谓的提升是指将声明提前到作用域顶部的行为，下面两个例子特别容易混淆。

```JavaScript
// 例子一
var value = 123;
(function(){
  if(typeof value === 'undefined'){
    value = 456;
  } else {
    value = 789;
  }
  console.log(value);
})()

//例子二
var name = 'World!';
(function () {
  if (typeof name === 'undefined') {
    var name = 'Jack';
    console.log('Goodbye ' + name);
  } else {
    console.log('Hello ' + name);
  }
})();
```

结果是：`Goodbye Jack`

### String，Number，Boolean 构造函数

通常我们都会使用字面量的写法来写`字符串`或者`数值`，而这些构造函数一样也可以创建`字符串`，他们与基本类型有着很大的不同。

- `new`出来的实例会一直存在，而基本类型在背后自动包装的会在代码执行后立即销毁
- 构造函数是存在着`prototype`，这也意味着你可以添加属性和方法

```JavaScript
function searchString(val){
  switch (val) {
    case 'A':
        console.log(1)
      break;
    case String('A'):
        console.log(2);
      break;
    default:
        console.log(3);
   }
}
searchString(new String('A'));
```
结果是：3

### apply，call，bind 的使用

虽然它们是三个不一样的`名字`，但是它们有着相同的目的，改变一个函数的作用域。

```JavaScript
console.log(Math.max.apply(null,[1,34,53,2,3,4]));
```
从一个数组中查找最大数，`Math.max`接收任意一个参数来比对，这个时候`apply`方法就非常有用了。

结果：`53`

```JavaScript
var obj = {
  x:1,
  y:2
};
function body(x,y){
  return this.x + this.y
}
var g = body.call(obj,2,3);
console.log(g);
```

`call`与`apply`类似，但它指接受单个参数的传递

结果：`3`

bind()方法会创建一个新函数，当这个新函数被调用时，它的this值是传递给bind()的第一个参数, 它的参数是bind()的其他参数和其原本的参数。

```JavaScript
var obj = {
   x:1,
   y:2
}
function body (y){
   console.log(y);
   console.log(this);
}
var _body = body.bind(obj,1);
console.log(_body(2))
```
结果：`1`

### 取余运算

一般来说对`x`取余如果可以返回0，如果不行返回1，也就是你需要取余的数可以被`x`整除，如果是负数，则跟随第一个操作数。

```JavaScript
function isOdd(num) {
    return num % 2 == 1;
}
function isEven(num) {
    return num % 2 == 0;
}
function isSane(num) {
    return isEven(num) || isOdd(num);
}
var values = [7, 4, '13', -9, Infinity];
values.map(isSane);
```
当这个题被换算之后可以是这样：

	7%2 == 1  true
	4%2 == 0 true
	'13'%2 == 1 true
	-9%2 == 1 || -9%2 == 0 false
	Infinity % 2 NaN  false
	
结果：`[true,true,true,false,false]`

### 数组

```JavaScript
var ary = Array(3);
ary[0]=2
ary.map(function(elem) { return '1'; });
```
数组的初始长度为`3`，但没有初始化，运算时map函数会跳过无初始化的坑。

结果：`["1",undefined x 2]`

```JavaScript
var arr = Array(3);
arr.push(4);
console.log(arr);
console.log(arr.length);
console.log(arr.slice(1).length);
```
跟上题类似，初始化长度为`3`，使用了`push`方法在数组末尾添加了一个值为`4`，长度增长为`4`，最后调用了`slice`方法，从下标`1`开始到最后一个元素，长度为`3`。

结果：`4`，`3`

### 数值运算

```JavaScript
3.toString()
3..toString()
3...toString()
```
在JS中`3.`，`.3`，`3`都是合法的数字，那么在解析`3.`的时候`.`到底是数字还是函数调用呢？显然是数字，你可以想象一下`3toString()`怎么会不报错？

结果：`error,"3",error`

```JavaScript
'5' + 3
'5' - 3
```
对于`+`我相信很好理解，如果都是数字则加运算，如果有字符串则是拼接。那么减号则会尽可能将两个操作数字变成数字来运算。

结果：`53 2`

### getPrototypeOf

`Object.getPrototypeOf`方法返回指定对象的原型（也就是该对象内部属性[[Prototype]]的值）。

```JavaScript
var a = {}, b = Object.prototype;
[a.prototype === b, Object.getPrototypeOf(a) === b]
```

而我们知道prototype只有`Function`才拥有`prototype`属性，可见`a.prototype`为`undefined`。

结果：`[false,true]`

### parseInt

```JavaScript
["1", "2", "3"].map(parseInt)
```
首先`map`接受两个参数分别是回调函数和回调函数的`this`值，而`parseInt`只接受两个参数分别是字符串和基数。

`map`的回调函数又接受三个参数分别是：`currentValue, index, arrary`，到这里其实就很好解释了：

```JavaScript
parseInt("1",0)
parseInt("2",1)
parseInt("3",2)
```

parseInt的基数是从`2~36`范围内，明显可以看到后两个不合法。

结果：`[1,NaN,NaN]`

### 类型检查 typeof instanceof

```JavaScript
[typeof null, null instanceof Object]
```
首先我们必须要清楚`typeof`和`instanceof`是做什么用的，前者会返回一个类型的字符串，后者会检查`constructor.prototype`是否存在于参数的Object.prototype上。

虽然`null`表示一个空值，但是它会返回一个`object`字符串

结果：`["object",false]`

### 在开发过程中遇到的让你印象最深刻的问题

相信这是一个老生常谈的问题，工作了这么多年总有些让你印象深刻的，这里只是告诉你问答的方式，没有可靠的答案。

- 我们用了react，开发方式印象很深刻
- 我们用了react而且使用了`shouldComponentUpdate`来优化
- 我们用了react，当你存在三个元素其中一个元素经常变动，而另外两个元素不会变动的情况，为了减少`render`方法的调用（因为它还会内部diff），我们会在`shouldComponentUpdate`方法中对属性进行了一些检查工作，确定了真正变动的情况下再重新渲染。

可见第三个回答会很受欢迎。

### Array.isArray

```JavaScript
Array.isArray( Array.prototype )
```

一个鲜为人知的实事: Array.prototype是一个数组。

结果：`true`