---
layout: post
title: Docker学习-入门
description: 初步了解docker及使用
keywords: mac,docker
categories: bigbang
---

<span class="impo">Docker</span>，2015最火的开源项目之一。

关于Docker-“快速部署”、“隔离”、“镜像”、“容器”这些关键词想必你一定听过。Docker可以将你的基础配置和应用服务隔离开来，打包你的环境配置并实现快速部署。通过“镜像”，我们可以快速的将一个应用部署到多个服务器上，而“容器”则是用来承载这些应用的。

本篇文章主要介绍如何使用docker，创建自己的镜像，运行容器等。具体使用准则参考官方[文档](https://docs.docker.com/)。

###你需要知道的一些基本概念

#####镜像-Image

镜像可以理解为应用的一个快照。里面保存着该应用运行所需要的各个配置、依赖、环境参数等。镜像还有一个非常关键的概念便是可以<span class="impo">叠加</span>。镜像使用了一种叫[union file system](https://en.wikipedia.org/wiki/UnionFS)的技术，将不同镜像按照层级叠加起来（可以理解成一种依赖关系）。


#####容器-Container

docker的容器则可理解为一个基础版的Linux系统。容器会根据镜像中的配置、资源在镜像的上层再添加一个应用运行的读写层。

![docker-filesystem](/images/docker-filesystems.png)

###安装

以下是<span class="impo">mac os</span>系统安装流程，[Linux](https://docs.docker.com/linux/step_one/) [Windows](https://docs.docker.com/windows/step_one/)

Docker本身并不支持直接在Mac OS上运行，不过Docker社区提供了一个工具boot2docker（实际是在Mac OS上创建一个虚拟机）。目前官方已将boot2docker整合到了官方工具<span class="impo">Docker Toolbox</span>中。

下载[Docker Toolbox](https://github.com/docker/toolbox/releases/download/v1.10.1/DockerToolbox-1.10.1.pkg)

具体安装流程可参考[Docker Mac OS 安装](https://docs.docker.com/mac/step_one/)

###运行

注意：直接在终端是无法运行docker的，需要进入boot2docker中。

Docker官方提供了一个类似github的Image管理仓库，你可以像使用github一样下载一份别人的Image。

我们下载一个 <span class="impo">alexwhen/docker-2048</span> 镜像作为示例

{% highlight javascript %}
docker pull alexwhen/docker-2048
{% endhighlight %}

![docker-images](/images/docker-images.png)

<span class="impo">docker images</span> 列出本地所有可用的Image，包括镜像名、TAG、创建时间和大小等信息。

现在我们可以用 <span class="impo">alexwhen/docker-2048</span> 镜像运行一个容器。

{% highlight javascript %}
docker run -d -p 8080:80 alexwhen/docker-2048
{% endhighlight %}

<span class="impo">-d</span> 让Container以后台进程运行，<span class="impo">-p</span> 指定8080端口的请求转发到Container的80端口。

通过 <span class="impo">docker ps</span> 检查我们容器是否运行正常。

![docker-ps](/images/docker-ps.png)

如何访问我们的应用呢？注意在Mac OS下，我们的docker是运行在boot2docker里的，所以需要链接虚拟机地址才能访问docker中的应用。

退出boot2docker 执行 <span class="impo">docker-machine ls</span>

![docker-machine-ls](/images/docker-machine-ls.png)

显示boot2docker地址 <span class="impo">192.168.99.100</span> 访问 <span class="impo">http://192.168.99.100:8080</span>

![docker-2048](/images/docker-2048.png)

###Container内部

下面我们可以进入Container，来看看Container内部是如何运作的。

{% highlight javascript %}
docker run -ti -p 8080:80 alexwhen/docker-2048 /bin/sh
{% endhighlight %}

应用代码

![alexwhen/docker-2048](/images/code-2048.png)

nginx配置

<span class="impo">vi /etc/nginx/nginx.conf</span>

{% highlight javascript %}
server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }
}
{% endhighlight %}

当容器以 <span class="impo">alexwhen/docker-2048</span> 镜像启动时，会启动一个nginx并监听Container的80端口。

当我们访问 <span class="impo">http://192.168.99.100:8080</span> 时，boot2docker会将8080端口的请求转发到Container的80端口，进而访问到应用。

