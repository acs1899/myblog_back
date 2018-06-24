---
layout: post
title: 在mac中使用cron
description: 在mac osx 中使用 crontab 注意事项
keywords: mac,crontab
categories:
- Linux/Mac
---

常用的cron命令:

{% highlight bash %}
  crontab -e //编辑任务
  -l //显示已有任务
  -r //删除所有任务
{% endhighlight %}

在mac下可能会遇见无法添加任务的情况

{% highlight bash %}
  crontab: "/usr/bin/vi" exited with status 1
{% endhighlight %}

这是因为环境变量<span class="impo">$EDITOR</span> 是 <span class="impo">vi</span>

重新设置成 <span class="impo">vim</span> 就行了


