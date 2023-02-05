---
title: MAC M1重启后自动启动虚拟机
categories:
  - MacOS
  - tools
tags:
  - MacOS
  - tools
abbrlink: 17bf4594
date: 2023-02-05 10:42:26
---

## Why
笔者在M1通过UTM setup了一个虚拟机，虚拟机开机的时候会自动启动我设置的服务，那如何让M1重启后自动启动虚拟机呢？ 
<!--more-->

## How
首先要找到通过命令启动虚拟机的方法：

 - UTM
 - - `/Applications/UTM.app/Contents/MacOS/utmctl start ubuntu --hide`
 - Virtualbox
 - - `/Applications/VirtualBox.app/Contents/MacOS/VBoxManage startvm ubuntu --type headless`

因为M1上virtualbox 不支持，我用的UTM去setup的虚拟机，具体过程详见 [here](https://blog.csdn.net/u011563903/article/details/127667082)。

创建自启动plist文件
```bash
cat /Users/haofan/Library/LaunchAgents/org.utm.launch.ubuntu.plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>org.utm.launch.ubuntu.plist</string>
  <key>ProgramArguments</key>
  <array>
    <string>/Applications/UTM.app/Contents/MacOS/utmctl</string>
    <string>start</string>
    <string>ubuntu</string>
    <string>--hide</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
</dict>
</plist>
```
测试：

```bash
launchctl load /Users/haofan/Library/LaunchAgents/org.utm.launch.ubuntu.plist
```
最后重启Mac后，虚拟机自动启动。
