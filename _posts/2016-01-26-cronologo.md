---
layout: post
title: 日志分割
description: 用cronolog分割Nginx日志
keywords: cronolog,nginx
categories:
- Linux/Mac
---

## 日志分割
bizfe平台的nginx日志由于一直写在一个文件里，长年累月后导致该日志文件过于臃肿，对日志数据的查找和分析带来很多不便。

#### 工具
cronolog日志分割工具

#### 安装
    wget https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/vps/cronolog-1.6.2.tar.gz
    tar zxvf cronolog-1.6.2.tar.gz
    cd cronolog-1.6.2
    ./configure
    make
    make install

#### 创建日志管道
    cd /home/service/nginx/logs
    mkfifo /home/service/nginx/logs/bizfe.access.pipe.log
    mkdir pipe /*用来存放分割后的日志*/

#### 修改nginx配置&重启
    access_log logs/bizfe.access.pipe.log;
    ./nginx -s reload

#### 启动cronolog
以下命令将日志以天为单位分割

    nohup cat /home/service/nginx/logs/bizfe.access.pipe.log | nohup /usr/sbin/cronolog /home/service/nginx/logs/pipe/bizfe.access.%Y%m%d.log &
