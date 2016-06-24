title: 事件监听器
date: 2016-02-22 12:10:07
tags: JavaScript专栏
---

事件监听器对于“订阅发布”模式，非常有用，在我们常用的一些事件中，比如`click`，也可以看做为一种“订阅发布”模型。今天除了写一写自定义的事件监听器，也想和大家一起复习一下Web前端的事件系统。

**复习浏览器事件**

事件顺序有两种类型：

- 事件捕获
- 事件冒泡

关于浏览器的历史原因，大家可以Google一下补脑；事件冒泡，从事件自身开始传播，一直到不确定的事件目标。而事件捕获正好与此相反，从不具体的事件目标，传播到事件的自身。而我们经常使用的DOM标准事件模型，则同时支持这两种顺序。但是，它的顺序是先发生捕获，再冒泡。

```JavaScript
    addEventListener(type,handler,bool)
```

**事件捕获使用场景**

根据*事件捕获*的特点来说，特别和操作系统有些相似，如果我想做全局的事件监听，比如document下所有的子节点，在某个条件下点击无效。这种情况就非常适合*事件捕获*来实现：

```JavaScript
    var doc = document;
    var cancel = true;
    doc.addEventListener('click',function(e){
        if(cancel){
            e.stopPropagation();
            e.preventDefault();
        }
    },true)
```


**事件冒泡使用场景**

比如在`onmousemove`这高频繁的场景中，可以进一步通过取消事件冒泡，打断它向上传播。

## 自定义事件监听器

在前几年，大家都在使用jQuery的时候，特别喜欢它实现的*事件系统*，包括有on注册，once注册一次，off解除，trigger触发几个操作。有了事件监听器，我们可以根据这个功能实现很多有用的场景，比如一个应用的某些状态，是根据用户的行为而改变的，但是这个行为却又是我无法预期的，通过事件监听器，可以辅助我们处理好这样的业务。

有些时候，我们可能需要一个单实例，那么once就有用武之地了。

```Swift
    //Swift 版的单例
    class var sharedInstanceManager: ValiantCenterManager {
        struct centerStatic {
            static var onceToken:dispatch_once_t = 0
            static var instance:ValiantCenterManager? = nil
        }
        dispatch_once(&centerStatic.onceToken) { () -> Void in
            centerStatic.instance = ValiantCenterManager()
        }
        return centerStatic.instance!
    }
```

**那么如何一步一步实现事件监听器**

假设我们有一个Event类，在这个类中的内部有一个this._events = []数组属性，它是用来装载所有的自定义事件。

*首先，我们需要实现的是$on注册事件方法。*

```JavaScript
Event.prototype.$on = function(event,fun){
        let cbs = this._events[event]
        cbs ? cbs.push(fun) : this._events[event] = []
        if (!cbs) {
            this._events[event].push(fun)
        }
        return this
}
```

这里为什么要将this._events设计为二维数组？因为事件可以是多个，但是事件名可能相同。这个逻辑意图非常的明显，根据event参数从this._events中获取是否存在。如果不存在，创建一个event为key的数组，并将事件句柄程序push到数组中。

*我们还需要实现$trigger触发事件方法*

```JavaScript
        Event.prototype.$trigger = function(event){
            let isString = typeof event === 'string'
            event = isString ? event : event.name
            let cbs = this._events[event]
            let args = tools.toArray(arguments,1)
            if (cbs) {
                let i = 0
                let j = cbs.length
                for(;i<j;i++){
                    let cb = cbs[i]
                    cb.apply(this,args)
                }
            }
        }
```

逻辑依然非常简单，通过事件名从this._events获取相应的事件句柄程序数组，然后将arguments转成数组，（这里考虑的是可能会传入参数）如果事件句柄程序数组存在，进行循环，再讲args参数apply给每一个取出来的事件句柄程序。

*我们最后需要$off解除事件方法*

```JavaScript
    Event.prototype.$off = function(event,fun){
        let cbs = this._events[event]

        //事件列队中无事件
        if (!cbs) {
            return this
        }

        //删除所有的事件
        if (!event && !fun) {
            this._events = {}
            return this;
        }

        //只有事件名称时
        if (event && !fun) {
            this._events[event] = null
            return this
        }

        //删除某个事件队列中的某个事件
        let cb;
        let i = cbs.length
        while(i--){
            cb = cbs[i]
            if (cb === fun || cb.fun === fun) {
                cbs.splice(i,1)
                break
            }
        }
        return this
    }
```

解除事件方法的逻辑可能相比之下稍许多了些，比如只存在事件组key名的情况，或者删除某个事件队列中的某个事件句柄程序。

**有了它之后对于前端的工作有哪些帮助？**

我可以想象到，在做手机版的SAP应用时，基于router的生命周期，就需要自定义事件监听器的帮助。使用它，其实你也了解了一种编程模型：“订阅发布”模式或者“通知机制”。当然，还有一些其他的场景。事件，对于前端而言，是一个非常需要掌握的技能。不管是DOM标准事件，还是自定义的事件监听器。
