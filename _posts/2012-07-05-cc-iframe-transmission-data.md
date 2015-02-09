---
layout: post
title: 跨域iframe数据传递
description: 解决与第三方应用通信的问题
categories: javascript
---
转载此文，方便以后查阅

###先看一下我们遇到了什么问题？###

在我们的白社会里，需要嵌入第三方应用，而嵌入的方式是使用 iframe，为了页面美观，这里就有一个最简单的需求：iframe 的高度需要跟随其本身内容的变化而实时变化，这就要求主页面根据 iframe 的内容实时的去设置其样式 height 值，但是因为第三方应用和白社会不属于同一个域，所以给实现带来了一点小小的麻烦，所以才有以下的一些讨论…

###仔细分析一下问题的实质是什么呢？###

其实这里需要解决的是，在一个页面 A 中嵌入一个iframe B，A 和 B 不属于同一个域，但是 A 和 B 需要进行一些必要的通信，传递少量的数据信息，所以问题的实质就是主页面与跨域 iframe 之间怎么通信，也就是怎么传递数据信息

下面就针对两种不同的需求，总结一些比较简单，常用和稳定的解决方案。

* 主页面A 怎么向 iframe B 传递数据
* iframe B 怎么向 主页面A 传递数据

###需求一：主页面A 怎么向 iframe B 传递数据呢？###

这种方式，是主页面需要给 iframe B 传递数据，然后 iframe B 获得到数据后进行特定的处理

实现的技巧就是利用 <span class="impo">location 对象的 hash 值，通过它传递通信数据，我们只需要在主页面A中设置 iframe B 的 src 后面多加个 #data 字符串</span>（data就是你要传递的数据）

然后在 iframe B 中通过某种方式能即时的获取到这儿 data 就可以了，其实常用的一种方式就是：

* 在 iframe B 中通过 setInterval 方法设置定时器， 监听 location.href 的变化即可获得上面的 data 信息
* 然后 iframe B 就能根据这个 data 信息进行相应的逻辑处理

###需求二：iframe B 怎么向 主页面A 传递数据呢？###

这种方式，是 iframe B 需要给主页面传递数据，然后主页面根据获得到数据后进行特定的处理

实现的技巧就是利用一个代理 iframeC，它嵌入到 iframe B 中，并且和主页面A必须保持是同域，然后我们通过它充分利用上面第一种通信方式的实现原理就能把 iframe B 的数据传递给 iframeC，接下来的问题就是怎么让iframeC把数据传递给主页面A

因为，iframeC 和主页面是同域的，所以它们之间传递数据就变得简单多了，我们这里的方式就是使用一个经常使用的属性 window.top (也可以使用window.parent.parent)，它返回对载入浏览器得最顶层 window 对象的引用，这样我们就能直接条用主页面A中方法啦，

###到此，我们做个简单分析总结###

当然还有其他一些方式，也都测试过，不是浏览器兼容性不好，就是实现起来复杂，通过以上方式就能很方便的在跨域的 iframe 和主页面之间传递数据了，当然也就能解决上面提到的设置 iframe 高度的问题了，但是这种实现方式的前提也是最大的缺点就是 iframe 中的内容必须是我们可控的，但是至少我们这种实现方式是建立在浏览器的安全规则之上的，没有破坏应用本身的安全性。

###实现时需要考虑的一些细节###

上面的分析，其实只是一个简单的原理，在白社会里，虽然我们目前的需求还仅仅是实现第三方 iframe 形式的 App 的高度自适应，但是我们在实现的时候尽量考虑到了易用，可扩展性和可维护性，比如：

* 让第三方 App 只需加载一个我们提供的JS种子文件就能很方便的使用我们为其提供的各种工具
* 上面的各种工具，我们采用包的形式进行组织，最大化的实现按需加载
* 第一条中的JS种子文件只提供基础的方法实现，并且把最常用的工具包放在里面，比如高度自适应
* 通过种子文件，我们还提供给第三方 App 一些常用的JS工具包，而且直接使用的类似YUI3模块的动态加载机制就可使用指定的工具包
* 对第三方 App 和 主页面传递的数据进行分类（自我调用，登录验证，传递数据等等）
* 传递的数据使用满足特定规范的JSON格式，并通过统一的服务出口发出去，主页面提供一个统一服务接口解析数据，并根据规范调用相应的方法
* 还有，就是版本控制的问题，为了尽量减少给第三方App带来影响，以上所有这些JS文件的版本都是采用向后兼容的策略，小版本使用服务器设置SQUID缓存特定频率的失效时间实现，大版本更新根据用户自己的需求手动更改
* 当然，以上可能不是最优的解决方案，只是希望能给你一些帮助和引导，我们也在逐步的改进我们的一些实现方式，比如版本控制这块儿，我们也有一些问题需要解决

###主页面A的源码###

{% highlight html  %}
<script type="text/javascript">
function init(){
    document.domain = 'bai.sohu.com';
    alert('我是主框架，嵌入了第三方应用IframeB,下面开始加载应用');
    var iframeTag = document.getElementById('frameB'),
        iframeSrc = 'http://test.com/iframePage.html';
    iframeTag.src = iframeSrc;
    iframeTag.style.display = 'block';
}

function callback(h){
    var iframeB = document.getElementById('frameB');
    alert('IframeC调用我（主框架）接口，把IframeB的高度传给我，具体值是：' + h);
    iframeB.style.height= h + 10 + 'px';
    iframeB.src += '#'+ h;
}
</script>
<body onload="init();">
    <p>我是主页框架，我的域是：bai.sohu.com</p>
    <iframe id="frameB" style="display:none;"></iframe>
</body>
{% endhighlight %}

###iframeB的源码###

{% highlight html  %}
<script type="text/javascript">
function init(){
    alert('我是第三方App，下面开始创建和主框架同域的通信通道IframeC,并设置它的src，用#号传递高度值');
    var iframeTag = document.getElementById('frameC'),
    iframeSrc = 'http://bai.sohu.com/iframePageC.html#',
    pageHeight = document.documentElement.scrollHeight || document.body.scrollHeight;
    iframeTag.src = iframeSrc + pageHeight;
    iframeTag.style.display = 'block';

    window.setTimeout(function(){
        alert('主页面设置我（IframeB）的src，通过Hash（#）给我传递它收到的高度：' + location.hash);
    },2000);
}
</script>
{% endhighlight %}

###iframeC的源码###

{% highlight html  %}
<script type="text/javascript">
document.domain = 'bai.sohu.com';
alert('我（IframeC）收到iframeB通过参数（#）给我传递高度值，我现在调用主页面方法去设置IframeB的高度');
top.callback(window.location.href.split('#')[1]);
</script>
{% endhighlight %}
