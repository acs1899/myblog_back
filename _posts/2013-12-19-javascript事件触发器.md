---
layout: post
title: javascript事件触发器
description: 事件触发器就是用来触发某个元素下的某个事件，IE下fireEvent方法，高级浏览器（chrome,firefox等）有dispatchEvent方法
keywords: fireEvent,dispatchEvent,IE
categories: javascript
---
转载此文方便以后查阅

事件触发器就是用来触发某个元素下的某个事件，IE下<span class="impo">fireEvent</span>方法，高级浏览器（chrome,firefox等）有<span class="impo">dispatchEvent</span>方法。

一般我们在元素上绑定事件后，是靠用户在这些元素上的鼠标行为来捕获或者触发事件的，或者自带的浏览器行为事件，比如click，mouseover，load等等，有些时候我们需要自定义事件或者在特定的情况下需要触发这些事件。这个时候我们可以使用IE下fireEvent方法，高级浏览器（chrome,firefox等）有dispatchEvent方法。

在IE下：
{% highlight javascript linenos %}
//document上绑定自定义事件ondataavailable
document.attachEvent('ondataavailable', function (event) {
    alert(event.eventType);
});
var obj=document.getElementById("obj");
//obj元素上绑定click事件
obj.attachEvent('onclick', function (event) {
    alert(event.eventType);
});
//调用document对象的createEventObject方法得到一个event的对象实例。
var event = document.createEventObject();
event.eventType = 'message';
//触发document上绑定的自定义事件ondataavailable
document.fireEvent('ondataavailable', event);
//触发obj元素上绑定click事件
document.getElementById("test").onclick = function () {
    obj.fireEvent('onclick', event);
};
{% endhighlight %}

[fireEvent的官方文档](http://msdn.microsoft.com/en-us/library/ms536423(v=vs.85).aspx)

[createEventObject的官方文档](http://msdn.microsoft.com/en-us/library/ie/ms536390(v=vs.85).aspx)

再看看高级浏览器（chrome,firefox等）：
{% highlight javascript linenos %}
//document上绑定自定义事件ondataavailable
document.addEventListener('ondataavailable', function (event) {
    alert(event.eventType);
}, false);
var obj = document.getElementById("obj");
//obj元素上绑定click事件
obj.addEventListener('click', function (event) {
    alert(event.eventType);
}, false);
//调用document对象的 createEvent 方法得到一个event的对象实例。
var event = document.createEvent('HTMLEvents');
// initEvent接受3个参数：
// 事件类型，是否冒泡，是否阻止浏览器的默认行为
event.initEvent("ondataavailable", true, true);
event.eventType = 'message';
//触发document上绑定的自定义事件ondataavailable
document.dispatchEvent(event);

var event1 = document.createEvent('HTMLEvents');
event1.initEvent("click", true, true);
event1.eventType = 'message';
//触发obj元素上绑定click事件
document.getElementById("test").onclick = function () {
    obj.dispatchEvent(event1);
};
{% endhighlight %}

原文地址：[javascript事件触发器fireEvent和dispatchEvent](http://www.css88.com/archives/4998)
