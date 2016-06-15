title: CSS--浅析REM
date: 2016-06-14 16:47:13
tags: CSS3
---

CSS开发中，时常会遇到em、rem、vh等等一些单位，在项目实践里却由于对它们的不熟悉以及不确定性而没有经常地的使用，今天有机会和大家一起认识其中的REM。

### 初识 REM
> In Cascading Style Sheets, the em unit is the height of the font in nominal points or inches. The actual, physical height of any given portion of the font depends on the user-defined DPI setting, current element font-size, and the particular font being used.

> To make style rules that depend only on the default font size, another unit was developed: the rem. The rem, or root em, is the font size of the root element of the document. Unlike the em, which may be different for each element, the rem is constant throughout the document.

先看一下[维基百科][1]对CSS中的REM定义，在CSS样式表中，单位em是作为字体高度的单位来使用的，但实际字体大小的高度显示是用户对DPI的定义来决定的。为了改善这种样式规则，单位rem则是直接取决于文档根元素字体默认大小，也可以理解为root em，跟em有所不同的是使用rem单位的字体大小在整个文档中都是恒定不变的

> <strong>Font-relative lengths: the em, ex, ch, rem units</strong>
Aside from rem (which refers to the font-size of the root element), the font-relative lengths refer to the font metrics of the element on which they are used. The exception is when they occur in the value of the font-size property itself, in which case they refer to the computed font metrics of the parent element (or the computed font metrics corresponding to the initial values of the font property, if the element has no parent).

看看[W3C][2]的定义，rem指的是根元素的字体大小，[MDN][3]对rem的解释也是一样。REM在CSS中基本概念是字体相对大小的长度单位，同EM、PX相似，但又有着千丝万缕的关系

### REM使用及注意事项
在rem使用之前，先得捋清楚单位em、rem和px之间的关系，特别是每一个单位的使用跟代码块和继承之间的关系。
A. http://codepen.io/jeremychurch/pen/rCcIh
b. http://codepen.io/jeremychurch/pen/dlyqw
参考上面两个简单的栗子，第一个里面的字体大小依次在减小，而第二个里面的字体却恒定没有变化，相对比会发现只是单位使用不一样的但效果是截然不同的。rem和em都是相对单位，px则不是。
为什么会产生这样不同的效果呢，答案需要回到rem的定义上。单位rem是相对根元素字体大小的，根元素是指body或者html，如果没有声明根元素字体大小，那么单位rem默认是等于16px;[这里][7]可以进行em、rem和px的换算。在实际项目中使用单位rem是需要这样定义的。
```css
body { font-size:62.5%; }  /* =10px */
h1   { font-size: 2.4em; } /* =24px */
p    { font-size: 1.4em; } /* =14px */
li   { font-size: 1.4em; } /* =14px */
```
```css
html { font-size: 62.5%; }  /* =10px */
body { font-size: 1.4rem; } /* =14px */
h1   { font-size: 2.4rem; } /* =24px */
```
```css
html {
    font-size: 62.5%;
}
body {
    font-size: 14px;
    font-size: 1.4rem;
}
h1 {
    font-size: 24px;
    font-size: 2.4rem;
}
```
<p data-height="265" data-theme-id="dark" data-slug-hash="ZOYVLY" data-default-tab="html" data-user="darcyWang" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/darcyWang/pen/ZOYVLY/">ZOYVLY</a> by TianbaoWang (<a href="http://codepen.io/darcyWang">@darcyWang</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>
### REM 深入

使用em的好处，响应式布局下的字体大小变化
```css
/* Only px units */
html { font-size: 16px; }
h1 { font-size: 33px; }
h2 { font-size: 28px; }
h3 { font-size: 23px; }
h4 { font-size: 19px; }
small { font-size: 13px; }
.box { padding: 20px; }
@media screen and (min-width: 1400px) {
html { font-size: 20px; }
h1 { font-size: 41px; }
h2 { font-size: 35px; }
h3 { font-size: 29px; }
h4 { font-size: 24px; }
small { font-size: 17px; }
.box { padding: 25px; }
}
/* Use em units */
html { font-size: 1em; }
h1 { font-size: 2.074em; }
h2 { font-size: 1.728em; }
h3 { font-size: 1.44em; }
h4 { font-size: 1.2em; }
small { font-size: 0.833em; }
.box { padding: 1.25em; }
@media screen and (min-width: 1400px) {
  html { font-size: 1.25em; }
}
```

上面展示了单位em在媒体查询中的使用，代码量相当于单位px的一半！用到的是单位em的继承和
相对父级(例子里面的html)字体大小。当单位em字体大小是相对直接或者是最近的父级，但单位rem的大小则是相对于html(根元素)字体大小，也可以把它理解为一种样式重置。那为什么要使用rem呢，这就像问你Less、Sass和Postcss到底哪个更好一样，举个栗子：margin: 50px 0;和margin: 5rem 0;在很多国外程序员写出的css中，不管是写sass或者是stylus的，他们更偏向于后一种写法，还有当看到别人的样式表中用着不同的单位，会不会有一种逼格的赶脚。
对em、rem和px的使用也不是绝对孰好孰坏，最好的方式就是同时使用，在对的地方使用对的单位，之前有人在[stackoverflow][11]问过使用相对单位，个人推荐多使用单位em，可以用px来写border，rem则可以用margin或者padding之类的，不过还是需要慎用[IE9,10][8]都不完全支持rem，给大家推荐一个sublime的[插件][15]

<p data-height="265" data-theme-id="dark" data-slug-hash="XKJxxY" data-default-tab="html" data-user="darcyWang" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/darcyWang/pen/XKJxxY/">CSS rem</a> by TianbaoWang (<a href="http://codepen.io/darcyWang">@darcyWang</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>
[1]: https://en.wikipedia.org/wiki/Em_(typography)#CSS
[2]: https://www.w3.org/TR/css3-values/#font-relative-lengths
[3]: https://developer.mozilla.org/en-US/docs/Web/CSS/length
[4]: https://www.sitepoint.com/understanding-and-using-rem-units-in-css/
[5]: http://snook.ca/archives/html_and_css/font-size-with-rem
[6]: https://j.eremy.net/confused-about-rem-and-em/

[7]: http://pxtoem.com/
[8]: http://caniuse.com/#feat=rem
[9]: https://css-tricks.com/theres-more-to-the-css-rem-unit-than-font-sizing/
[10]: http://snook.ca/archives/html_and_css/font-size-with-rem
[11]: http://stackoverflow.com/questions/11799236/should-i-use-px-or-rem-value-units-in-my-css
[12]: http://css3files.com/2012/10/11/relative-is-the-new-absolute-the-rem-unit/
[13]: https://en.wikipedia.org/wiki/Em_(typography)#CSS
[14]: https://en.wikipedia.org/wiki/Em_(typography)#CSS
[15]: https://github.com/flashlizi/cssrem


喜欢的话，记得关注我们的微信号哦！
![](https://raw.githubusercontent.com/icepy/_posts/master/img/weixin.jpg)
