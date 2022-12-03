---
title: 用Tinyproxy搭建自己的proxy server
categories: tools
tags: tools
abbrlink: 32acde64
date: 2022-12-03 23:35:57
---
## What is Tinyproxy
[Tinyproxy](https://tinyproxy.github.io) 是一个轻量级，跨平台，开源的，同时支持http/https两种方式代理。
<!--more-->

## 为什么搭建自己的proxy server
设想这种case, 你家里有一台电脑可以登录各种网站(细品)，然后你家里的其他设备，电脑，ipad也想登录各种网站，怎么办？

一个简单的办法，是可以把能登录各种网站的电脑通过Tinyproxy配置一个http/https proxy server，然后其他终端设备就可以在局域网里直接连接这个http/https server了。

## 如何安装/配置proxy server
这里只讲mac平台的配置。

安装tinyproxy，一条命令搞定
```bash
brew install tinyproxy
```
配置tinyproxy，修改如下文件：

```bash
vi /opt/brew/etc/tinyproxy/tinyproxy.conf
# 修改 Allow 127.0.0.1
# 如果comment Allow 127.0.0.1 就是默认允许任何主机访问。
# 可以修改成如下，意思是只允许局域网内访问。
Allow 192.168.0.0/16
```
启动tinyproxy
```bash
tinyproxy # run in background
tinyproxy -d # Do not daemonize (run in foreground)
```
## 如何配置其他手机/电脑连接proxy server
* iPhone手机。设置->无线局域网->点击你已经连接的wifi后的叹号图标->配置代理->选择手动->输入IP地址和端口号。
* Mac。选择wifi -> Network preferences -> Advanced -> proxies -> enable web proxy(http)和Secure web proxy(https) 输入ip地址和端口号即可。

上面说的IP地址就是你能上各种网站的电脑的局域网内IP，一般是192.168.x.x。