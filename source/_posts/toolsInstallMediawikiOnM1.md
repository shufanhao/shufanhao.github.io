---
title: 在运行在M1的ubuntu虚拟机上安装Mediawiki
categories:
  - MacOS
  - tools
tags:
  - MacOS
  - tools
abbrlink: d8b155f3
date: 2023-02-05 10:40:03
---

## Why
最近开始转向arm架构的M1 Macbook了，不得不说M1的续航，性能确实用起来很丝滑。之前在x86上安装的虚拟机以及虚拟机上安装的mediawiki都要迁移到M1上。

因为底层系统架构从x86到ARM，所以之前的虚拟机不能在M1上直接用，并且docker image也必须用arm64版本的，一切都要从头开始。
<!--more-->
Mediawiki已经成为我日常工作记录笔记的重要的工具，用它建立知识树很方便，并且search也很快，要想把mediawiki 跑起来，要setup一个VM，然后在VM上用docker 将mediawiki setup。

安装mediawiki之前，先安装ubuntu或者centos VM，[参考](https://blog.csdn.net/u011563903/article/details/127667082) 。

## 安装
参考 [here](https://hub.docker.com/r/arm64v8/mysql)

### 安装mysql
初始化创建目录

```bash
mkdir -p $HOME/mywiki/mysql/initdb.d
mkdir -p $HOME/mywiki/mysql/data
# 创建mysql.cnf
cat $HOME/mywiki/mysql/initdb.d/mysql.cnf
# This is custom config file attached from docker host

[mysql]
default_character_set = utf8

[mysqld]
character_set_server = utf8          # If you prefer utf8
collation_server = utf8_general_ci
```

```bash
# 创建run-mysql.sh，然后跑下这个脚本，mysql就setup 起来了。
cat $HOME/mywiki/mysql/run-mysql.sh
echo "stopping mysql container"
docker stop mysql 2> /dev/null

echo "removing mysql container"
docker rm mysql 2> /dev/null

echo "re-starting mysql container"
docker run \
	--name mysql \
	-v $HOME/mywiki/mysql/initdb.d:/docker-entrypoint-initdb.d \
	-v $HOME/mywiki/mysql/data:/var/lib/mysql \
	-e MYSQL_ROOT_PASSWORD=changeme \
	-d arm64v8/mysql:8-oracle \
```
### 安装mediawiki
参考 [here](https://hub.docker.com/r/arm64v8/mediawiki/)

初始化创建目录

```bash
mkdir -p $HOME/mywiki/mediawiki/config
mkdir -p $HOME/mywiki/mediawiki/resources/assets/
mkdir -p $HOME/mywiki/mediawiki/images/
chmod 755 $HOME/mywiki/mediawiki/images/
```

```bash
# 创建run-mediawiki.sh，然后跑下这个脚本，mediawiki就setup 起来了。
cat $HOME/mywiki/mediawiki/run-mediawiki.sh

echo "Stopping mediawiki container"
docker stop mediawiki 2> /dev/null

echo "Removing mediawiki container"
docker rm mediawiki 2> /dev/null

echo "Restarting mediawiki container"
docker run --name mediawiki \
	--link mysql:mysql \
	-p 8080:80 \
	# After initial setup, download LocalSettings.php to the same directory and uncomment below code, then restart mediawiki
	# -v $HOME/mywiki/mediawiki/config/LocalSettings.php:/var/www/html/LocalSettings.php \
	-v $HOME/mywiki/mediawiki/images:/var/www/html/images \
	-v $HOME/mywiki/mediawiki/resources/assets/mywiki.jpg:/var/www/html/resources/assets/mywiki.jpg \
	-d arm64v8/mediawiki:1.35.9 \
```

docker ps 检查两个container是否都正常运行起来了。

```bash
haofan@haofan:~/mywiki/mediawiki$ docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                                   NAMES
259197fb1486   arm64v8/mediawiki:1.35.9   "docker-php-entrypoi…"   37 seconds ago   Up 36 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   mediawiki
cd448396fc1f   arm64v8/mysql:8-oracle     "docker-entrypoint.s…"   37 minutes ago   Up 37 minutes   3306/tcp, 33060/tcp                     mysql
```
浏览器访问http://192.168.64.8:8080/index.php 开始setup mediawiki。192.168.64.8 是我的虚拟机IP

![在这里插入图片描述](https://img-blog.csdnimg.cn/3aea7437222041b893de056396c05248.png)

数据库的主机，就是mysql container的ip，可以通过。`docker inspect mysql | grep "IPAddress"` 查看IP
<img src="https://img-blog.csdnimg.cn/5f2f3b5f926c457bb3ae883f02d5c9a3.png" width="50%">

输入用户名和密码:
<img src="https://img-blog.csdnimg.cn/9bba294889024fb199aca485632713b9.png" width="50%">

启用文件上传：
<img src="https://img-blog.csdnimg.cn/f568090eb57640efb49fbd9b024b75ec.png" width="50%">

下载LocalSettings.php，放到$HOME/mywiki/mediawiki/config下。然后修改LocalSettings.php

```bash
# Before
$wgServer = "http://192.168.64.8:8080";
# After
$wgServer = "http://0.0.0.0:8080";
```
重启mediawiki，脚本用:

```bash
cat $HOME/mywiki/mediawiki/run-mediawiki.sh

echo "Stopping mediawiki container"
docker stop mediawiki 2> /dev/null

echo "Removing mediawiki container"
docker rm mediawiki 2> /dev/null

echo "Restarting mediawiki container"
docker run --name mediawiki \
	--link mysql:mysql \
	-p 8080:80 \
	-v $HOME/mywiki/mediawiki/config/LocalSettings.php:/var/www/html/LocalSettings.php \
	-v $HOME/mywiki/mediawiki/images:/var/www/html/images \
	-v $HOME/mywiki/mediawiki/resources/assets/mywiki.jpg:/var/www/html/resources/assets/mywiki.jpg \
	-d arm64v8/mediawiki:1.35.9 \
```
再次打开，wiki 已经安装完成，start enjoying. 

## 迁移
不得不一个个page进行迁移，在M1上运行如下命令:

```bash
# 192.168.2.143 是x86的MacOS。任何访问localhost:8080的流量，都会访问192.168.2.143:8080
 ssh haofan@192.168.2.143 -L 8080:localhost:8080
```
这样就可以在M1上打开在x86上跑的旧的mediawiki了。然后开始慢慢迁移吧。

## 设置systemd让虚拟机重启时自动启动wiki
设置systemd.service
```bash
# systemd service:
# cat /etc/systemd/system/mywiki.service
[Unit]
Description=Mywiki Service
Requires=docker.service
After=docker.service

[Service]
Restart=false
ExecStartPre=/bin/sleep 5
ExecStart=/home/haofan/mywiki/startWiki.sh

[Install]
WantedBy=default.target
```

```bash
# Start wiki 脚本
# cat /home/haofan/mywiki/startWiki.sh
#!/bin/bash
/usr/bin/docker start mysql
/bin/sleep 2
/usr/bin/docker start mediawiki
```
enable mywiki.service，然后重启VM测试:

```bash
sudo systemctl daemon-reload
sudo reboot
```
