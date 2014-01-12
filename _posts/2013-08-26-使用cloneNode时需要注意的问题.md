---
layout: post
title: 使用cloneNode时需要注意的问题
description: 当被clone的元素包涵script子元素时，script中的脚本是否会被执行多次。当被clone的元素被绑定了事件时，clone后的元素是否还能触发绑定的事件
keywords: cloneNode,event
categories: javascript
---
oneNode()是DOM中Node对象的方法，使用cloneNode可以方便的复制DOM节点。cloneNode()接收一个参数<span class="impo">include\_all</span>。<span class="impo">include\_all</span>为一个布尔值，true表示被clone的节点的所有子节点也会被clone（既深度clone），false(默认)只会clone原节点。

1、当被clone的节点包含script标签时，clone后script标签是否会再次被执行

<p style="color:#ff8c00">内嵌script标签：</p>
{% highlight html linenos %}
<div id="box">
    <script type="text/javascript" >alert(1)</script>  
</div>  
<script type="text/javascript">  
    document.body.appendChild(document.getElementById('box').cloneNode(true));  
</script>
{% endhighlight %}
在所有浏览器中<span class="impo">alert</span>都只执行一次

<p style="color:#ff8c00">外链script标签：</p>
{% highlight html linenos %}
<div id="box">
    <script type="text/javascript" src='clone.js'></script>  
</div>  
<script type="text/javascript">  
    document.body.appendChild(document.getElementById('box').cloneNode(true));  
</script>
{% endhighlight %}
在非IE浏览器中<span class="impo">alert</span>只执行一次

在IE中，只有IE6会执行两次

<p style="#00bfff">解决方法：在将clone后的节点加入DOM前，手动删除掉里面的script标签</p>


2.当被clone节点被绑定了事件处理函数时，事件处理函数是否会被一同clone

<p style="color:#ff8c00">HTML事件处理绑定：</p>
{% highlight html linenos %}
<div id="box" onclick='alert(1)'>点我</div>  
<script type="text/javascript">  
    document.body.appendChild(document.getElementById('box').cloneNode(true));  
</script> 
{% endhighlight %}
在所有浏览器中，click事件均被复制

<p style="color:#ff8c00">DOM0级事件处理绑定：</p>
{% highlight html linenos %}
<div id="box">点我</div>  
<script type="text/javascript">  
    var box=document.getElementById('box');
    box.onclick=function(){alert(1)}  
    document.body.appendChild(box.cloneNode(true));  
</script>
{% endhighlight %}
在所有浏览器中，点击第一个div会有<span class="impo">alert</span>，点击第二个div无反应

<p style="color:#ff8c00">DOM2级事件处理绑定：</p>
{% highlight html linenos %}
<div id="box">点我</div>  
<script type="text/javascript">  
    var box=document.getElementById('box');
    if(box.attachEvent){  
        box.attachEvent('onclick',function(){alert(1)},false)  
    }else{  
        box.addEventListener('click',function(){alert(1)})  
    }  
    document.body.appendChild(box.cloneNode(true));
</script>
{% endhighlight %}
在非IE浏览器下 点击第二个div不会执行<span class="impo">alert</span>

但是在IE6、7、8中 点击第二个div则会执行<span class="impo">alert</span>

在《精通javascript》一书中，作者推荐一种Dean Edwards提出的跨浏览器事件绑定/删除事件解决方案
{% highlight javascript linenos %}
function addEvent(element, type, handler) {  
    // 为每一个事件处理函数赋予一个独立ID
    if (!handler.$$guid) handler.$$guid = addEvent.guid++;
    // 为元素建立一个事件类型的散列表（元素的所有事件类型都保存在该对象中）
    if (!element.events) element.events = {};
    // 为每一个元素/事件建立一个事件函数处理的散列表(同一事件类型的不同处理函数保存在该对象中)
    var handlers = element.events[type];
    if (!handlers) {
        handlers = element.events[type] = {};
        // 存储已有事件处理函数（如果存在一个）
        if (element["on" + type]) {
            handlers[0] = element["on" + type]; 
        }
    }
    // 在散列表中存储该事件类型的处理函数
    handlers[handler.$$guid] = handler;
    // 注册一个全局处理函数来处理所有函数
    element["on" + type] = handleEvent;
}

// 创建独立ID计数器
addEvent.guid = 1;                                                

function removeEvent(element, type, handler) {
    // 从散列表中删除事件处理函数
    if (element.events && element.events[type]) {
        delete element.events[type][handler.$$guid];
    }
};
                                                            
function handleEvent(event) {
    // 获取event对象 (IE 中使用全局event对象)
    event = event || window.event;
    // 获取对应事件的处理函数散列表
    var handlers = this.events[event.type];
    // 依次执行处理函数散列表中的函数
    for (var i in handlers) {
        this.$$handleEvent = handlers;
        handlers[i].apply(this,[event]);
    }
};
{% endhighlight %}

<p style="color:#00bfff">为了弥补element['on'+type]无法绑定多个处理函数的缺点，addEvent将所有事件类型存储在element的events对象中，events中的每一个事件类型以同样的形式存储着该类型下所有的处理函数</p>
{% highlight javascript linenos %}
element.events={
    click:{
        0:function(){...},
        1:function(){...},
        ...
    },
    mousedown:{...},
    ...
}
{% endhighlight %}


总结：

1.在使用cloneNode()时，最好在插入前将clone出来的节点中的script手动清除掉，以避免脚本可能被重复执行。

2.使用cloneNode()会将通过attachEvent绑定的事件复制到clone出来的节点上，可以通过使用跨浏览器的事件绑定解决方案来统一让绑定的事件不被复制。
