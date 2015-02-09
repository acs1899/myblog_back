---
layout: post
title: getElementById方法的作用域
description: IE6~8下document.getElementById方法内部实现与其他浏览器（包括IE9及以上）不同
keywords: getElementById,作用域
categories: javascript
---
在看jQuery内核详解一书时，为了比较jQuery与原生javascript效率，举到一个例子。

里面有这样一段：
{% highlight javascript  %}
var $=document.getElementById;
var b=$('header');
{% endhighlight %}
秉着'实践出真理'的原则，自己敲一敲代码跑了一下。

在chrome、firefox、IE9下运行时，会抛出错误：

<span class="impo">Illegal invocation</span> chrome

<span class="impo">'getElementById' called on an object that does not implement interface Document.</span> firefox

再到IE6~8下，却能够顺畅运行。

<span class="impo">Illegal invocation</span>意为非法调用

<span class="impo">'getElementById' called on an object that does not implement interface Document.</span>意为'"getElementById"被一个不能实现document接口的对象调用'（乱翻译的，不过大致意思应该是这样。。。）

说明肯能是作用域的问题
{% highlight javascript  %}
var $=document.getElementById;
var b=$.call(document,'header');
alert(b);
{% endhighlight %}
这次在非IE6~8下成功运行

总结：

在IE6~8下document.getElementById与作用域无关，也就是说在何处调用不会影响函数本身。

在其他浏览器（包括IE9及以上）document.getElementById与作用域有关，也就是说getElementById函数内部this必须指向<span class="impo">document
</span>。<span class="impo">getElementsByTagName</span>也有同样的情况。
