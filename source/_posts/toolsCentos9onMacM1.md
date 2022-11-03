---
title: 一文搞定centos VM 在MAC M1安装
categories: tools
tags: tools
abbrlink: fc62d617
date: 2022-11-03 15:04:23
---

自从买入Macbook M1 之后，在MAC上安装centos虚拟机一直是想尝试的事情。

最近，virtualbox 的最新的beta 版本说是已经支持在m1上运行，结果今天测试了一下发现并不work。

然后就折腾其他方案，终于work了。本文描述的是通过UTM + centos9 实现在Mac M1上启动centos VM。

<!--more-->
## 下载安装UTM
[UTM](https://mac.getutm.app) 是一个开源的在MacOS/IOS上基于QEMU启动虚拟机的方案。

[下载link](https://github.com/utmapp/UTM/releases/latest/download/UTM.dmg) 

安装直接点击下载后的文件，即可。
## why centos9
我试过安装centos7，但是发现，centos7不能安装成功。具体讨论在，[here](https://github.com/utmapp/utm/discussions/2659)。

结论就是：centos7/centos8不能在Mac M1下运行成功，centos9可以。

centos9 [下载地址](https://mirrors.bfsu.edu.cn/centos-stream/9-stream/BaseOS/aarch64/iso/CentOS-Stream-9-latest-aarch64-dvd1.iso)，必须是aarch64 版本。
## 安装centos9
安装比较简单:

### Step1: Create VM. 
<img src=https://img-blog.csdnimg.cn/9c47886478444817ab621c3565916612.png width=60% />

### Step2: Select ISO image
ISO image 是: CentOS-Stream-9-latest-aarch64-dvd1.iso
<img src=https://img-blog.csdnimg.cn/af18397ffc5d413f959ec353b3ac0334.png width=60%/>

然后根据自己需要设置以下参数:

 - Memory: 4GB 
 - Cores: 4 
 - Disk Size: 64GB

Next step, step... 设置disk 和 admin username/password

### Reboot 
选择clear ISO image，否则重启后，又要重新安装centos
<img src=https://img-blog.csdnimg.cn/ccdf833ca0f24c129ab685ed40869aa5.png width=60%>

Reboot后，enjoy centos9.