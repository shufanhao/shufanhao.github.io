---
title: vercel建站配置DNS
categories: hexo
tags: hexo
abbrlink: 544375c0
date: 2022-05-29 14:26:38
---
如果还没有github pages，请参考 [here](https://shufanhao.top/posts/c5404504/). 
## Why use Vercel ? 
1. 国内访问github pages比较慢，有时候出现加载的问题。
2. baidu不能爬到github pages的页面，即使设置了baidusitemap，原因是: 2015年，因为一些不能细说的原因，Github 开始拒绝百度爬虫的访问，直接返回 403。官方给出原因是，百度爬虫爬得太狠，影响了 Github Page 服务的正常使用。这就导致了，但凡在 Github Page 搭建的个人博客，都无法被百度收录。
<!--more-->
So vercel 可以解决以上问题。

### What is Vercel ? 
vercel类似于github page，但远比github page强大，速度也快得多得多，全球很多节点帮你缓存。

而且将Github授权给vercel后，可以达到最优雅的发布体验，只需将代码轻轻一推，项目就自动更新部署了。

vercel还支持部署serverless接口。那代表着，其不仅仅可以部署静态网站，甚至可以部署动态网站，而这些功能，统统都是免费的.

vercel还支持自动配置https，不用自己去FreeSSL申请证书，更是省去了一大堆证书的配置。

## How to use Vercel ? 
1. Access [https://vercel.com/dashboard](https://vercel.com/dashboard)，并且授权github
2. New project
3. Import github project
	<img src=https://img-blog.csdnimg.cn/b10a42f86ddf41dc848aff3b346244ac.png width=60% /> 
4. Select Framework preset. 这里我选择的是other，就是一个静态网站。因为我repo的master branch 是放的blog的静态网站，而hexo branch放的是Hexo source 文件。
<img src=https://img-blog.csdnimg.cn/60886e50e21b4367aa1ea68e50a0e4e8.png width=60% /> 

默认会从github repo的default branch 拉取代码，一旦import后，可以在project -> setting -> git -> Production Branch 设置 branch name.

导入结束后，如果master branch 有任何change，就会trigger vercel 的deployment。

并且在部署成功后，会申请一个默认的域名，我的是shufanhao-github-io.vercel.app。

这个域名太长了，那如何自己定义一个简单域名呢？ 
## How to apply DNS Domain ?
天下没有免费的午餐。如果需要一个自己定义的DNS domain，那只能花钱了。

我自己调研了一下，目前.top的二级域名，稍微便宜点。比较了阿里云和腾讯云，基本价格差不多，所幸直接在阿里云买了shufanhao.top的10年是189块钱。

![在这里插入图片描述](https://img-blog.csdnimg.cn/7011bd89f8a44832bf789d71bff821ae.png)

买了之后，就可以根据阿里云的文档一步一步操作了，需要实名认证，人工审核等，大概需要个半天的时间。
## How to configure Vercel/Aliyun DNS ? 

### Configure Vercel
输入刚申请的domain，shufanhao.top，然后点击add: 
<img src=https://img-blog.csdnimg.cn/831070a105f34562b98d8bdb6137e154.png width=60% /> 

我是选择的第二个，因为我想让网址默认显示shufanhao.top而不是www.shufanhao.top。

<img src=https://img-blog.csdnimg.cn/7afad74476d64b1e8945b5f98ebc12ea.png width=50% /> 

配置完后，会发现域名解析失败，根据提示到DNS provider 再做设置。
### Aliyun 
阿里云设置就是添加一个A记录和CNAME。
![在这里插入图片描述](https://img-blog.csdnimg.cn/b308df7759b344a6968fd3a71084e8bc.png)

设置完成后，再去查看vercel domains 发现都是正常了。


<img src=https://img-blog.csdnimg.cn/1e94953e409c4ddcb24901fb4d030229.png width=60% /> 

至此，我也有了一个自己的域名了。

欢迎访问[https://shufanhao.top](https://shufanhao.top) 和我的个人公共号。

![请添加图片描述](https://img-blog.csdnimg.cn/44f9447660ec41899a56f1a934d98a6a.jpeg)
