title: 数组的函数式编程
date: 2016-06-24 22:07:53
tags: JavaScript专栏
---

如果我们将函数式编程浓缩成一句话：那么它就是一个求值的过程。数组是我们经常要使用到的一种数据结构，利用函数式的思路，可以帮助我们减少大量的求值操作所带来的代码量。

> 至少它在视觉上让我很满意

**求和运算**

```JavaScript
//bad
var data = [1,2,3,4];
var k;
for(var i=0;i<data.length;i++){
  if(!k){
    k = data[i]
  }else{
    k+= data[i]
  }
}

//good
var val = data.reduce((pre,cur)=>{
  return pre+cur
});

```

利用`reduce`可以直接执行求和运算，比之上面的`for`循环要好看的多。

**筛选数据再求和运算**

```JavaScript
//bad
var data = [12,2,2,3,4,5,1];
var result = [];
var k;
for(var i = 0;i<data.length;i++){
  if (data[i] === 2) {
    result.push(data[i]);
  }
}
for(var j = 0;j<result.length;j++){
  if(!k){
    k = result[j];
  }else{
    k+= result[j];
  }
}

//good

var k = data.filter((v)=>{
  return v === 2
})
.reduce((pre,cur)=>{
  return pre+cur;
})

```

这里利用了返回值是数组的特性，使用链式的方式来运行筛选数据之后的求和运算。

**map函数的运用**

```JavaScript
var numbers = [1,4,9];
var roots = numbers.map(Math.sqrt);
```

使用`map`方法加上`sqrt`可以直接求元素的平方根，如果更运用一个更复制的例子：

```JavaScript
//bad

var words = ['wow','icepy','wow'];
var result = [];
words.forEach(function(v){
  if(v==='wow'){
    result.push(v.toUpperCase());
  }
});

//good

var result = words.filter((v)=>{
  return v === 'wow'
})
.map((v)=>{
  return v.toUpperCase();
})

```

**浅复制一段数据**

```JavaScript
var data = [
  {
    val:1
  },
  {
    val:2
  },
  {
    val:3
  }
]
var result = data.slice(1);
```

**更接近“函数式”编程风格的代码**

```JavaScript
function factorial(n){
  if (equal(n, 1)){
    return 1;
  } else {
    return mul(n, factorial(dec(n)));
  }
}
```

**拼接数据**

```JavaScript
var data = [
  {
    val:1
  },
  {
    val:2
  }
]

//bad

var result = [];
data.forEach(function(v){
  result.push('<p>'+v.val+'</p>');
});
result.join('');

//good
var html = data.map((v)=>{
  return '<p>'+v.val+'</p>';
})
.join('');
```

当然在数组中还有更多技巧有待我们的挖掘，你呢？

## 关注我们

**扫二维码** 或搜索 **fed-talk** ，关注我们的微信公众号。

<div align="center">
<img src="https://raw.githubusercontent.com/icepy/_posts/master/img/weixin.jpg" alt=""/><br>
</div>
