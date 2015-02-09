---
layout: post
title: IE6、7下 body标签overflow:hidden失效的问题
description: IE6 IE7下不生效(IE6下横向纵向滚动条都在 IE7下纵向滚动条还在)
keywords: IE6 IE7 overflow
categories: html&css
---
{% highlight html  %}
<p>There are no scrollbars on this page in sane browsers</p>
{% endhighlight %}
{% highlight css  %}
html, body, p {margin: 0; padding: 0;}
body {overflow: hidden;}
p {width: 5000px; height: 5000px;}
{% endhighlight %}
<span class="impo">IE6 IE7下不生效(IE6下横向纵向滚动条都在 IE7下纵向滚动条还在)</span>

原因：

明智的浏览器(ex. chrome and firefox)会初始付值给html<span class="inpo">{overflow:visible;}</span>

IE6 初始付值html<span class="impo">{overflow-x:auto;overflow-y:scroll;}</span>

IE7 初始付值html<span class="impo">{overflow-x:visible;overflow-y:scroll;}</span>

只有dom根结点（也就是html根节点）设置html<span class="impo">{overflow:visible;}</span>的时候，浏览器才会将body元素中的overflow值应用到视图区。

举个例子说：

设置了body<span class="impo">{overflow:hidden}<span>还会出现滚动条，不过这个滚动条不是body的，是html的

只有你设置html<span class="impo">{overflow:visible;}</span> body<span class="impo">{overflow的值}</span>才能传递到html中去

这样html的值就变成了<span class="impo">{overflow:hidden}</span>ok没有滚动条了

这样就很明了啦，并不是bug，而是浏览器初始值不同产生的问题。
