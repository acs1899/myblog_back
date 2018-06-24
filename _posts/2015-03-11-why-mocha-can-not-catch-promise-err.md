---
layout: post
title: promise中的错误捕获
description: promise捕获错误后不会直接抛出错误，而是传递给后续回调链
keywords: nodejs,Promise,test
categories:
- javascript
---

接[上篇](http://acs1899.info/javascript/2015/03/09/node-test-promise.html)文章留下的一个没有解决的问题：**在promise中的错误为什么在外层函数捕获不到？**

来看下面一个栗子：

{% highlight javascript %}
    var o = {};

    function A(){
        try{
            o.show();
        }catch(err){

        }
    }

    function B(){
        try{
            A();
            console.log("你看不见我")
        }catch(err){
            console.log(err)
        }
    }

    B();//输出结果："你看不见我"
{% endhighlight %}

上面的栗子说明 <span class="impo">try/catch</span> 在捕获到错误后阻止了错误向上传递。你只能手动抛出错误让上层函数捕获。

{% highlight javascript %}
    var o = {};

    function A(){
        try{
            o.show();
        }catch(err){
            throw err
        }
    }

    function B(){
        try{
            A();
            console.log("你看不见我")
        }catch(err){
            console.log(err)
        }
    }

    B();//输出结果："[TypeError: Object #<Object> has no method 'show']"
{% endhighlight %}

再看看文章开头提到的问题 **在promise中的错误为什么在外层函数捕获不到？**

###真相只有一个

promise模块核心代码[core.js](https://github.com/then/promise/blob/master/lib/core.js):

{% highlight javascript %}
    ...
    /*直接跳至关键处*/
    var cb = state ? deferred.onFulfilled : deferred.onRejected
      if (cb === null) {
        (state ? deferred.resolve : deferred.reject)(value)
        return
      }
      var ret
      /* #A */
      try {
        ret = cb(value)
      }
      catch (e) {
        deferred.reject(e)
        return
      }
      /* #B */
      deferred.resolve(ret)
    ...
{% endhighlight %}

注意 <span class="impo">#A</span> 与 <span class="impo">#B</span> 之间的代码。没错就是它！！！这里 <span class="impo">catch</span> 错误后并没有将其抛出，而是传递给了下级 <span class="impo">reject</span> 并结束当前函数。

好了，现在菊势基本明了了。问题根源便是 <span class="impo">Promise</span> 始终劫持着错误对象没有抛出，导致外层函数捕获不到错误。

那么再往下看 <span class="impo">Promise.catch</span> 方法又是如何捕获错误的？

[es6-extensions.js](https://github.com/then/promise/blob/master/lib/es6-extensions.js):

{% highlight javascript %}
    Promise.prototype['catch'] = function (onRejected) {
        return this.then(null, onRejected);
    }
{% endhighlight %}

原来 <span class="impo">Promise.catch</span> 不过是 <span class="impo">Promise.then(null,reject)</span> 的缩写版。

其实 <span class="impo">Promise</span> 作者是建议在所有 <span class="impo">Promise</span> 对象后加上 <span class="impo">Promise.done</span> 方法。<span class="impo">Promise.catch</span> 方法只是为了与ES6标准规范保持一致而添加的。

[done.js](https://github.com/then/promise/blob/master/lib/done.js):

{% highlight javascript %}
    'use strict';

    var Promise = require('./core.js')
    var asap = require('asap')

    module.exports = Promise
    Promise.prototype.done = function (onFulfilled, onRejected) {
        var self = arguments.length ? this.then.apply(this, arguments) : this
        self.then(null, function (err) {
            asap(function () {
                throw err
            })
        })
    }
{% endhighlight %}

<span class="impo">Promise.done</span> 的作用与 <span class="impo">Promise.then</span> 基本一致（注意done并没有 <span class="impo">return</span> Promise对象，所以 <span class="impo">done</span> 只能放在最后），只是在后面加了一层抛出捕获到的错误。


