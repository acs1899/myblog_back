---
layout: post
title: 如何为使用Promise规范的接口写测试用例
description: 当接口使用Promise规范时，常规的异步测试写法不能正常捕获错误
keywords: nodejs,Promise,test
categories: javascript
---

nodeJs为Javascript引入了单元测试的概念。单元测试能够帮助我们更好的提高代码质量，让BUG在代码编写的时候即被消灭掉。

同步单元测试：

{% highlight javascript %}
    var should = require('should');/*断言库*/

    function A(x){
        return x ? true : false
    }

    describe('function A test',function(){
        it('when x = 1,return true',function(){
            A(1).should.be.true;
        });
        it('when x = 0,return false',function(){
            A(0).should.be.false;
        });
    });
{% endhighlight %}

异步单元测试：

{% highlight javascript %}
    var should = require('should');
    var fs = require('fs');

    describe('fs.readFile is ok',function(done){
        fs.readFile('/test.txt','utf-8',function(err,data){
            should.not.exist(err);
            done();
        });
    });
{% endhighlight %}

同步与异步测试区别就在于 <span class="impo">describe</span> 的回调函数是否有形参 <span class="impo">done</span>

<span class="impo">Promise</span> 规范的目的是为了让程序员能以同步代码的形式来写异步代码。直观的表现就是，本来无限嵌套的回调函数变成了链式调用。

<span class="impo">Promise</span>规范来书写的接口：

{% highlight javascript %}
    var Promise = require('promise');
    var fs = require('fs');
    /*Promise的实现方式有很多种，这里使用promise库*/

    function fileRead(filePath,encode){
        return new Promise(function(resole,reject){
            fs.readFile(filePath,encode?encode:'utf-8',function(err,data){
                if(err){
                    reject(err);
                }else{
                    resolve(data);
                }
            });
        });
    }

    /*调用*/
    fileRead('/test.txt')
        .then(function(data){
            //do something with data
            ...
        },function(err){
            //log err
            ...
        });

    /*测试*/
    var should = require('should');
    describe('fileRead is ok',function(done){
        fileRead('/test.txt')
            .then(function(result){
                result.should.be.String;
                done();
            },function(err){
                done(err);
            })
    });
{% endhighlight %}

跑一下测试。嗯，好像没问题。收拾东西下班！！！

###wait a minute

注意测试代码中这一行代码

{% highlight javascript %}
    result.should.be.String;
{% endhighlight %}

这行代码的目的是检测 <span class="impo">result</span> 是否是字符串。如果 <span class="impo">result</span> 不是字符串，那应该会被测试框架捕获到。

稍微修改下

{% highlight javascript %}
    [1,2,3].should.be.String;
    /*这里只是为了测试错误是否能被捕获到，所以用[1,2,3]替换了result*/
{% endhighlight %}

###纳尼！！！

测试如我所料没有通过，但是报的错误是 <span class="impo">timeout</span> 是什么鬼啊！！！

好像一不小心又发现了一个知识点啊。嗯，今天晚上一定要把它解决了。

兴奋的我带着电脑回到家中玩了一个晚上的魔兽。。。

明天周六，我花一天时间肯定能搞定！！！（这话估计也就我自己会信╮(╯▽╰)╭）

就这样，这个问题陪伴着我度过了一个愉快而又充实的3.8妇女节（什么鬼。。。摔）

周一上班的路上突然想到会不会跟 <span class="impo">promise</span> 的实现有关。来到公司，遂看了 <span class="impo">promise</span> 的core.js代码。

经过一番倒腾，大致理顺了 <span class="impo">promise</span> 的核心思路。 <span class="impo">promise</span> 是通过内部的一个handler构造函数来控制回调链的传递。并且你所传入的回调函数并非直接由handler控制，而是由两个叫 <span class="impo">resolve</span> 和 <span class="impo">reject</span> 的方法控制。

所以我猜测错误可能是被 <span class="impo">promise</span> 捕获了，但是没有被测试框架捕获。（不要问我为什么，我也不知道）

于是用尽了各种办法，最终还是没能证明上面的猜测（一股弱者的气息。。。）

###google大法好

这是[stackoverflow](http://stackoverflow.com/questions/24071493/should-js-not-causing-mocha-test-to-fail)上相同的问题，证实了我的猜测。

被采纳的答案其实也没能说清其中原由。

答案中引用的when.js的doc文档中也是一句话带过 <span class="impo">Errors in an asynchronous operation always occur in a different call stack than the the one that initiated the operation</span>

修改测试代码：

{% highlight javascript %}
    var should = require('should');
    describe('fileRead is ok',function(done){
        fileRead('/test.txt')
            .then(function(result){
                [1,2,3].should.be.String;
                done();
            },done).catch(done);
    });
{% endhighlight %}

顺利捕获断言错误。至于为什么<span class="impo">promise</span>阻断了测试框架捕获错误，还有待研究。
