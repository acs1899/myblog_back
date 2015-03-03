---
layout: post
title: Github搭建自己的blog
description: 在Github上创建Gitpage与安装jekyll
keywords: githubPage,jekyll
categories: [javascript, html&css, jekyll]
---

Github Page 本是为git项目提供一个静态说明页，后来被开发成了一个bolg系统。

###Step One###

####安装Ruby ####

安装rvm（ruby管理工具）
{% highlight javascript %}
curl -L https://get.rvm.io | bash -s stable
source ~/.rvm/script/rvm
rvm install requirements
{% endhighlight %}

安装ruby
{% highlight javascript %}
rvm install ruby
rvm use ruby --default
{% endhighlight %}

安装rubygems
{% highlight javascript %}
rvm rubygems current
{% endhighlight %}

安装rails
{% highlight javascript %}
gem install rails
{% endhighlight %}

####安装jekyll####

{% highlight javascript %}
gem install jekyll
{% endhighlight %}

OK~现在你已经成功安装jekyll了（如果不出什么意外的话）

那么来试试

{% highlight javascript %}
jekyll new myBlog
cd myBlog
jekyll server
{% endhighlight %}


然后你就能通过 <span class="impo">http://localhost:4000<span> 访问jekyll为你生成的blog了。

###Step Two###

在github上创建一个项目。进入项目setting

![setting](/images/s1.png)

![setting](/images/s2.png)

<span class="impo">Automatic page generator</span> 能帮你自动生成一些固定模板样式页面

在本地搞个blog项目，代码写得飞起~~~

当你觉得自己的blog已经完美之后，你可以通过<span class="impo">jekyll build</span>来让jekyll帮你生成最后的静态代码。这些代码会保存在./_site下。最后你也只需要将./_site下的文件pull到github上

{% highlight javascript %}
jekyll build
{% endhighlight %}

当然在你调试的过程中需要不停地预览你的blog，这时你应该会用到

{% highlight javascript %}
jekyll server
{% endhighlight %}

<span class="impo">jekyll server</span>包含了上面的<span class="impo">jekyll build</span>，之后会起一个本地服务通过<span class="impo">http://localhost:4000</span>来预览./_site中的页面

