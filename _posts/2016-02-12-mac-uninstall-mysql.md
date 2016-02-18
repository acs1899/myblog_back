---
layout: post
title: Mac下卸载mysql
description: mac下卸载DMG包安装的mysql
keywords: mac,uninstall,mysql
categories: bigbang
---

记录删除需要的操作，供以后查阅。

{% highlight ruby %}
sudo rm /usr/local/mysql
sudo rm -rf /usr/local/mysql*
sudo rm -rf /Library/StartupItems/MySQLCOM
sudo rm -rf /Library/PreferencePanes/My*
vim /etc/hostconfig and removed the line MYSQLCOM=-YES-
rm -rf ~/Library/PreferencePanes/My*
sudo rm -rf /Library/Receipts/mysql*
sudo rm -rf /Library/Receipts/MySQL*
sudo rm -rf /var/db/receipts/com.mysql.*
{% endhighlight %}