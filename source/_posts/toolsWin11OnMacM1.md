---
title: 一文搞定通过UTM 在MAC M1上安装Win11
categories:
  - MacOS
  - tools
tags:
  - MacOS
  - tools
abbrlink: fffdf12c
date: 2024-01-07 21:55:16
---

## Why 
临近过年，一年一度的抢票大战就要开始。抢票软件要求安装在windows，作为mac资深用户，必须安装个windows虚拟机。
<!--more-->

## How 

step by step: 
* follow [YouTube](https://www.youtube.com/watch?v=OwwGC3Gi_4g )。具体step follow [YouTube](https://www.youtube.com/watch?v=OwwGC3Gi_4g ) 视频。本文，只说一下，特别容易错的step。
* follow [getutm](https://docs.getutm.app/guides/windows/)

1.下载win11 arm 版ISO镜像，please refer [here](https://uupdump.net/known.php?q=windows+11+arm). 

2.开始安装

<center><img src="https://img-blog.csdnimg.cn/direct/c15b749a76404a83866f1bd101f5909a.png" width="60%"></center>

3.会碰到这个问题。PC can't run Windows11

<center><img src="https://img-blog.csdnimg.cn/direct/372e7fa892324e5da44540e21877ec00.png" width="60%"></center>

Follow: [This PC can’t run Windows 11](https://docs.getutm.app/guides/windows/#this-pc-cant-run-windows-11) to fix your issue. 

注意: shift + Fn + F10 打开terminal, 然后输入regedit打开注册表，修改后的注册表如下图：
<center><img src="https://img-blog.csdnimg.cn/direct/59afd9ab73ef4753801f523fc1342439.png" width="60%"></center>

修改完注册表后，再返回上一步，然后下一步，就可以看到如下图:
<center><img src="https://img-blog.csdnimg.cn/direct/50e1d424ddfd4bff9373ad7b81e3068c.png" width="60%"></center>

4.再会碰到如下问题。连接不了网络。
<center><img src="https://img-blog.csdnimg.cn/direct/6da9d8d23e85411d855dcea4371e3f2f.png" width="60%"></center>

如何解决呢：
shift + Fn + F10 打开terminal, 然后输入OOBE\BYPASSNRO，注意是大写的O，不是零。
<center><img src="https://img-blog.csdnimg.cn/direct/6fbb283e1ecf4fbf93e10b769e83982c.png" width="60%"></center>

然后系统会重新启动。

5.最后系统打开后，网络还是不能连接，原因是没有安装驱动。

打开drive image option: 
<center><img src="https://img-blog.csdnimg.cn/direct/3e40376a07f14ac5944f22865ddfb91f.png" width="60%"></center>

点击change: 找到下载好的驱动，即可。驱动[下载地址](https://docs.getutm.app/guest-support/windows/#download)。
<center><img src="https://img-blog.csdnimg.cn/direct/c1a44ad21dd34c2f846d8f9b76ab0de8.png" width="60%"></center>

都结束后就，重启VM，就可以正常连接网络。
