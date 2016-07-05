title: JavaScript异步编程一站式扫盲
date: 2016-07-05 12:51:01
tags: JavaScript专栏
---

> 一站式扫盲，让你读懂JavaScript的异步编程。

如果要说异步编程，其实我们可以先说一说多线程模型（这需要你用一个抽象的方式来看待这两个术语）。多线程可以将具体的任务分解成多个子任务，然后由系统来调度协调多个子任务的执行，所谓的异步编程将要解决的问题是如此。异步编程模型和多线程模型还有另外一个不同点，在多线程系统中，推迟一个任务的执行而去执行另一个任务大大的超出了程序员的控制。尽管，这个在操作系统的控制下，但程序员只能猜测一个任务被推迟，另一个任务来替代，可能在任何可能的时候发生。相比较，在异步模型中，一个任务会继续执行除非显示的放弃控制权给另一个任务，这说明异步编程要优于多线程编程的。

众所周知，JavaScript的执行环境是一个单线程模型，执行顺序从上到下依次执行，一次只能完成一个任务。这也意味着，做任何事情，你都需要排队，这明显是非常效率低下的，我们可以想象一下，如果你准备去上厕所：

```JavaScript
//嘿，哥们，还需要多久？
//十分钟后我叫你
//....
```

明显这十分钟你总不能在厕所门口干等着，回到之前的多线程模型，这个时候对于上厕所这个地方已经锁住了，于是你可以利用好这十分钟去做一点别的事情，完事之后，你回到厕所（是不是有种线程同步的感受？）。异步编程的道理也是如此，你上厕所这个动作是一个事务，你带着这个事务去做别的事情，这中间需要经历十分钟，然后你回到厕所继续执行这个事务。

## 回味一下最老的方式来

很久之前我们都知道可以使用`setTimeout`和`setInterval`来延迟执行，看起来这个又回到了前面所复述的场景，如果第二个去上厕所之前先去买纸和烟呢？

```JavaScript

setTimeout(function(){
//上厕所
},10)

//买纸和烟

var buy = '买纸和烟';

```

当然这里也可以使用`setInterval`每隔十分钟执行一次上厕所的事务，不过要是现实生活中真这样，估计你要好好去检查检查你的肾了。

当然某些情况下，你也可以使用`callback`回调函数的方式进行，最经典的当属`jQuery`给你提供的各种callback。

```JavaScript
$.ajax({
 url: '',
 type: 'GET',
 success: function (){
	
 },
 error: function (){

 }
})
```

在你使用`ajax`请求的时候，你就需要成功或者失败这两个回调，如果请求成功，那么这里就会执行`success`这个callback了。

```JavaScript

var cf = function (callback){
 var i = 1;
 var cb = callback()
 return i + cb;
}

cf(function(){
 return 2;
})

```

## 事件驱动

这应该是相对`callback`，`setTimeout`等方式的一种进化，任务的执行将不取决于代码的顺序，而是取决于事件何时发生（emit）。

众所周知浏览器提供了良好的事件模型来增强Web的交互性，可以想象这对于前端开发者来说，应该是不陌生的。如果我有一个事务，将在吃完晚饭之后执行：

```JavaScript
events.on('踢球',function(){});

// 吃晚饭的过程，应该包括做饭菜，刷碗，散步

var over = '吃晚饭';

events.emit('踢球');

```

## 观察者模式

在提到事件之后就不得不提到`观察者模式`了，假定我们存在一个中心化的源，当某个任务执行完成之后，就可以向这个中心源发布一个信号，而其它任务则可以向这个中心源订阅信号，从而知道自己可以什么时候开始执行事务。

```JavaScript
//首选踢球这个事务向中心源订阅

observer.watch('踢球',function(){
// 踢球完
 observer.remove('踢球')
})

//接着，我们继续开始做晚饭，刷碗，散步

function 吃晚饭(){
 observer.publish('踢球')
}

```

这种方式从代码上来看与事件驱动非常的类似，不过它明显的要优于后者。因为你通过中心源，可以查看存在着多少信号，每一个信号有多少个订阅者，从而监控你的应用程序。

## Worker

`Worker` 是HTML5引进的一个关于多线程的接口，利用它开发者也可以将一些重量级的计算单元分割成多个子单元，然后再另外的线程中执行。虽然看起来这是一个很异步编程不太相干的接口，如果你有好了它，也能达到同样的目的。

```JavaScript
var work = new Worker('bundle.js');
work.onmessage = function (event) {
 // event.data 数据同步
}

work.postMessage('');

```

当然这是一个非常简单的例子，如果我们创建多个Worker，在监听时还需要区分一下event.target源的来路。Worker 在某种情况下是一个非常好用的特性，我们可以在Worker中使用JavaScript语言带来的特性，当然除了DOM。

## Promise

`Promise` 是抽象异步处理对象及其各类操作的类，在ES6中正式的将其标准化了。

```JavaScript
function getURL(URL) {
  return new Promise(function (resolve, reject) {
      var req = new XMLHttpRequest();
      req.open('GET', URL, true);
      req.onload = function () {
         if (req.status === 200) {
           resolve(req.responseText);
         } else {
           reject(new Error(req.statusText));
         }
      };
      req.onerror = function () {
         reject(new Error(req.statusText));
      };
      req.send();
  });
}
```

`Promise` 就是如此将很多异步处理的流程进行了标准化，通过提供统一的接口来处理成功还是失败的状态。上面那个 `Ajax` 的例子，使用了Promise改造之后，在你使用 `Ajax` 来请求数据时，将不会存在更多的函数嵌套，这是将一些比较复杂的异步流程梳理成让人易于接受的。

```JavaScript
var promise = getURL('http://github.com/icepy')
promise.then(function onFulfilled (value) {

})

promise.catch(function onRejected (error){

})
```

## Generator es6

es6 提供了一个 ` Generator` 对象，本来这个对象的主要目的并不是来解决异步编程的问题，不过随着开发者的脑洞大开，我们利用它的某些特性来控制函数的执行状态，同样可以达到异步的目的（这是JavaScript版本的协程）。

`Generator` 函数使用一个 `*` 号来定义，内部的执行走向使用 `yield` 来控制。

```JavaScript
function *Ajax(){
 var url = 'http://github.com/icepy';
 var result = yield $.ajax(url);
}
var ajax = Ajax();
var result = ajax.next();
result.value.done(function(){

})
```

## Async Await

`Async/Await` 是ES7推出的关于解决异步编程的方案，目前来看它不管是在语义，还是理解程度上来看都要比之前的方案要简单的多。

```JavaScript
//定义async函数
var sleep = function () {
// 等待执行的逻辑
}
var start = async function () {
 console.log('start')
 await sleep()
 console.log('end')
}
```

唯一需要注意的地方是 `await` 必须存在于 `async` 定义的函数作用域中。当然多个 `async/await` 如果是在循环中，你且不需要担心以前的 `闭包` 问题了。

```JavaScript
(async () => {
  let files = await readFiles();
  // await只能使用在原生语法
  for (var file of files) {
    let name = path.parse(file).name;
    savePoster(name, await getPoster(name));
  }
})();
```

## 常见异步编程库

以上就是`JavaScript`的异步编程的方案，当然如果你这样原始的使用还是不行的，有很多细节还需要处理（如果你的项目足够复杂和庞大的话）。

目前社区里也有一些非常好用的库来支持异步编程：

- [Q](https://github.com/kriskowal/q)
- [co](https://github.com/tj/co)

当然，还有很多比较优秀的库，就不一一道来了。

## 内置福利

言归正传我们在微信群中推出了《早读课》，每日分享一篇我们认真精选的文章（不仅限于前端开发类的），其目的是帮助开发者来学习有价值的东西。想加微信群的朋友，直接添加我的微信号：icepy_1988，过后我会邀请你。

在后台回复福利，可以获取下载《全球移动技术大会2016》PPT集合。

另外安利个广告，我们很认真，严谨的提供了一个付费订阅服务《Mulgore Pro 订阅》，其目的是帮助前端开发者提升你的知识结构和JavaScript水平，在这里你可以获取在市面上看不到的内容和资源，有资深开发者帮助你Review你的代码和话题讨论。你可以点击菜单中间的Pro计划来获取详细的信息，我只能说一句：绝对的物有所值。

## 关注我们

扫二维码 或搜索 fed-talk ，关注我们的微信公众号，也欢迎你将它分享给自己的朋友。

<div align="center">
<img src="https://raw.githubusercontent.com/icepy/_posts/master/img/weixin.jpg" alt=""/><br>
</div>




