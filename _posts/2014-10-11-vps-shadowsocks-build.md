---
layout: post
title: VPS搭建ShadowSocks
description: VPS搭建ShadowSocks，翻墙手记
keywords: VPS ShadowSocks
categories: Linux/Mac
---

ShadowSocks是一个轻量级socks5代理，以Python2.x写成。之前用的是用pptp搭建的vpn。

我的手机是Nexus 4，几乎所有的Google服务都不能用(那还要亲儿子干嘛!!!)，所以自己搞个代理不能再等了。

ShadowSocks有很多中版本，<span class="impo">Python</span><span class="impo">Nodejs</span><span class="impo">Go</span><span class="impo">C</span>，我用的是<span class="impo">Python</span>版。

####安装Setuptools

{% highlight javascript  %}
wget --no-check-certificate https://pypi.python.org/packages/2.7/s/setuptools/setuptools-0.6c11-py2.7.egg
chmod +x setuptools-0.6c11-py2.7.egg 
./setuptools-0.6c11-py2.6.egg
{% endhighlight %}

####安装Python-pip

{% highlight javascript  %}
wget --no-check-certificate https://pypi.python.org/packages/source/p/pip/pip-1.4.tar.gz
tar -zxvf ./pip-1.4.tar.gz
cd pip-1.4
sudo python setup.py install
{% endhighlight %}

####安装Python-Gevent

{% highlight javascript  %}
sudo apt-get install libevent-dev
sudo apt-get install python-dev
pip install gevent
{% endhighlight %}

####安装Python-M2Crypto

{% highlight javascript  %}
sudo apt-get install libssl-dev
sudo apt-get install swig
pip install M2Crypto
{% endhighlight %}

####安装ShadowSocks-Python

{% highlight javascript  %}
pip install shadowsocks
{% endhighlight %}

####config.json

config.json是ShadowSocks Server端的配置文件

{% highlight html  %}
vim ~/ShadowSocks/config.json
{% endhighlight %}

config.json配置文件格式：

{% highlight javascript  %}
{
"server":"my_server_ip",//服务器IP
"server_port":8388,//服务器端口
"local_port":1080,//本地端口(配置客户端时需要用到)
"password":"barfoo!",//密码
"timeout":600,//超市时间
"method":"aes-256-cfb"//加密方法，推荐"aes-256-cfb"
}
{% endhighlight %}

####运行ShadowSocks程序

cd到config.json所在目录

{% highlight javascript  %}
nohup ssserver > log &
{% endhighlight %}

之所以选用ShadowSocks主要是看重其对客户端强大的支持，几乎所有你能想到的系统都用对应的客户端。

####客户端设置

客户端的配置几乎于配置文件的内容一样，只需要将配置文件中配置项对应填入即可。

使用之后，感觉比同等环境下的VPN快了不少。用手机到Google play上更新App尤为明显。

PS:用aws的同学注意 <span class="impo">config.json</span> 中的 <span class="impo">server</span> 要填内网地址
