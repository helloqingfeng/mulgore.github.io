title: 上传图片攻略全解
date: 2016-03-27 12:08:08
tags: JavaScript专栏
---

## 上传图片攻略全解

讲解分题：

- 传统的form表单上传
- 隐藏iframe模拟Ajax（跨域）上传
- HTML5特性实现Ajax上传
- 检校图片信息

### 传统的form表单上传

传统的上传图片，需要追溯到很久之前的时期。假设我们用`python`实现了一个简单的WSGI网页，那么我们需要使用form表单发送POST请求来提交到服务器，并且刷新当前页面。最早的HTTP实际上是不支持图片上传的，普通的HTTP POST请求，它会在头信息里使用Content-Length注明内容长度。头信息每行一条，空行之后便是body，即“内容”（entity）。它的Content-Type是application/x-www-form-urlencoded，这意味着消息内容会经过url编码，就像GET请求时url里的queryString那样。

```JavaScript
id=1&setting=will

```

从[《RFC 1867 - Form-based File Upload in HTML》](http://www.faqs.org/rfcs/rfc1867.html)提出之后，HTTP开始支持了图片上传。

```HTML
<form action="http://api.com/upload" method="POST" enctype="multipart/form-data">
	<input type="file" name="img">
</form>
```

改变`enctype`属性为`multipart/form-data`即可。

### 隐藏iframe模拟Ajax（跨域）上传

当我们实现了第一步时，这还不是最优的选择，为什么？因为我们的页面需要刷新，才能真正完成提交图片到服务器的交互。最开始，很多人会尝试想实现一个Ajax无刷新的上传图片，那么问题来了，如何实现？

这个时候利用iframe可以完成我们的需求，form的name对应iframe的target，可以将刷新目标转移到iframe，不过要注意，首先要设置iframe为隐藏。

```JavaScript
AjaxForm.prototype._addEvent = function(){
	var self = this;
	this._iframe.on('load',function(){
		if (self._iframeLoadState) {
			var cw = this.contentWindow;
			var loc = cw.location;
			if (loc.href === 'about:blank') {
				if (_.isFunction(self._failure)) {
					self._failure.call(this);
				};
			}else{
				if (_.isFunction(self._success)) {
					try {//如果后台没有作跨域处理，则需手动触发onComplete
						var body = this._iframe[0].contentWindow.document.body;
						innerText = body.innerText;
						if (!innerText) {
							innerText = body.innerHTML;
						};
						if (innerText) {
							self._success.call(this, $.parseJSON(innerText));
						};
					} catch (e) {
						self._success.call(this);
					}

				};
			};
			self._iframeLoadState = false;
		};
	});
};

```

### HTML5特性实现Ajax上传

新的HTML5特性对于上传，实现起来更容易了，FormData可以提交二进制数据。一般情况下，我们只需要将form对象装载到FormData中即可。

```JavaScript
var form = document.getElementById('form');
var formData = new FormData(form);
```

不过，有意思的是可以利用`append`方法可以添加一些更多的信息。

```JavaScript
var img = document.getElementById('img').files[0];
var formData = new FormData();
formData.append('img',img);
```

最后利用`XMLHttpRequest`来最后提交

```JavaScript
var xhr = new XMLHttpRequest();
xhr.open('POST','http://api.com/upload');
xhr.onreadystatechange = function(){};
xhr.send(formData);
```

有意思的是XHR2提供了一个很棒的事件给我们来获取上传的进度，比如：

```JavaScript
xhr.upload.onprogress = function(evt){
    console.log(evt)
    var loaded = evt.loaded;//已经上传大小情况
    var tot = evt.total;  //附件总大小
    var per = Math.floor(100*loaded/tot);  //已经上传的百分比  
}
```

### 检校图片信息

HTML5标签提供了一些有趣的属性来检索图片信息后缀，比如：

- accept：表示可以选择的文件MIME类型，多个MIME类型用英文逗号分开，常用的MIME类型见下表。
- multiple：是否可以选择多个文件，多个文件时其value值为第一个文件的虚拟路径。

相比之下，更有用的还是需要去获取file对象，例子：

```HTML
<input id="img" type="file" name="img" onchange="chans()"/>
```

```JavaScript
var img = document.getElementById('img')
var files = img.files
```

files对象是一个数组，这个数组可以关联到`实现多图片上传`的功能，当然，这里我们只针对一张图片的信息来说。

```JavaScript
var file = files[0]
```

file对象的信息：

- name 图片名
- size 图片大小
- type 图片类型
- lastModified 最后上传的时间毫秒

利用这些属性可以对即将上传的图片做一些校验，最起码不会将一个.txt的文件上传上去，还可以在前端规避一些文件大小，比如：只允许上传5MB的图片。
