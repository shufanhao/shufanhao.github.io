---
title: Tableau 入门系列之各种图形绘制
categories: BigData
tags: BigData
abbrlink: '1324e406'
date: 2022-12-01 23:19:27
---
## What 
Tableau 是一个可视化分析平台，它改变了我们使用数据解决问题的方式，使个人和组织能够充分利用自己的数据。

Tableau提供了非常丰富的图表，通过及其强大的功能，使得数据的可视化极其容易。Tableau，至少是我遇到最强大的可视化平台。

<!--more-->
## 熟悉Tableau
如何下载及安装这里不讲了。可以先用试用版，试着熟悉下tableau。讲几个重要的概念。

<img src="https://img-blog.csdnimg.cn/119d6e903cc946fdab647bd11fcafff3.png" width="500">

1. Data source。是用来连接各种数据，包括excel, 各种sql 数据库，no sql 数据库，spark，trino等。只有你想不到的数据，没有它连接不了的数据。想要做数据visulization的第一步就是要连接数据。连接数据后，也可以做一下数据的预处理。
2. Worksheet。是绘图的一个工作空间，最后制作dashboard的时候，就是由一个个Worksheet组成。
3. Dimensions(维度)。当连接到数据源的时候，Tableau将离散类型的字段(例如：值类型是字符串或者布尔值的字段)分配到Dimensions中。将Dimensions中的字段点击或拖拽进入行或者列当中时，Tableau就创建了行或列的标题。
4. Measures(度量)。当连接到某个数据源的时候，Tableau会将包含数字信息的字段分配到Measure上。当拖拽一个Measure字段到行或者列上时，Tableau会创建一根连续的轴。
5. Dashboard。就是最后通过拖拽Worksheet，构建dashboard，展示给用户的是最终的dashboard。
6. Marks(标记)。可以更改图的类型，颜色，文字等。这个是很重要的，通过修改mark来达到自己想要的效果。
7. Show me(智能推荐)。会根据你选择的Dimensions或者Measures，去推荐可以应用的图形。

## 绘制各种图形
Follow 这个视频系列：[https://www.bilibili.com/video/BV1yZ4y1W7YM/](https://www.bilibili.com/video/BV1yZ4y1W7YM/) 

视频所用到的超市数据：[link](https://download.csdn.net/download/u011563903/87222298)

完整的练习: [超市分析.twbx](https://download.csdn.net/download/u011563903/87229964)

另外能翻墙的话，youtube上有很多高质量的Tableau教学视频可以参考。

### 柱状图 📊
直接将利润拖拽到Rows，然后类别，子类别拖拽到Columns，然后子类别拖到color，就可以根据子类别显示颜色，效果图:
<img src="https://img-blog.csdnimg.cn/4ac3a481184d4fad9425c55235d1c9d5.png" width="400">

### 折线图📈
一张图显示两个measures, 销售额和利润。将利润和销售额拖到Rows，然后订单日期拖到Column，订单日期也可以更改成按quarter, week, day。

还要注意一点，因为Rows是两个，相当于两个图层，所以要选择Dual Axis的方式。
<img src="https://img-blog.csdnimg.cn/5c230ac0aef54673a83d23d1795612c0.png" width="400">

最后效果图：
<img src="https://img-blog.csdnimg.cn/9ccd2a6c1ab64d5bb5e3d14cd49f761a.png" width="400">

### 饼图
直接将字段和折扣拖到Marks里。

<img src="https://img-blog.csdnimg.cn/d4f93a628ac343cb97525f89e688325f.png" width="400">

### 文字云 
通过文字的颜色和大小展示数据的方式。

<img src="https://img-blog.csdnimg.cn/a3ad603dd41043bc885ea6797cc313b5.png" width="400">

### 气泡图
<img src="https://img-blog.csdnimg.cn/a1e4f92a0627447b9a2fce3e1156e1dd.png" width="400">

### 热图
通过图形的大小来展示数据
<img src="https://img-blog.csdnimg.cn/5afb7bd5c73d457fb6f2d6ce8f4e361d.png" width="400">

### 突出显示图
通过颜色的深浅来凸显数据。

<img src="https://img-blog.csdnimg.cn/c82ddd0bcf6d452c95013c74106e1967.png" width="400">

### 筛选器
可以将字段拖到filter里指定需要exclude或者选中的字段。

<img src="https://img-blog.csdnimg.cn/a2c6960eaceb426f8d95cf228c89b26a.png" width="400">

### 参考线
可以画出一根在图中的参考线。
<img src="https://img-blog.csdnimg.cn/ecc440572c384818aa36073a8293becf.png" width="400">

### 地图
如果选择城市，省等map数据后不能显示地图，有两个可能的原因：
1.选中字段省/自治区 —> Geographic Role -> State/Province，意思要让tableau知道这个字段是一个省的字段。
<img src="https://img-blog.csdnimg.cn/be176a2f3fb841d2b6dbb7d0a33a69ab.png" width="400">

2.如果你的系统版本是英文的，然后在选择制作地图的时候，内容是空的图层，解决方法是：Map ->  Edit Location -> Country/Region 改成China。反之英文的字段也有可能存在这个问题。并且点击感叹号旁边的1 issue,2issues 可以手动map tableau不能识别的数据。

<img src="https://img-blog.csdnimg.cn/e92758bf79c1434982ad64aaef8cc928.png" width="400">

开始绘制地图，想要显示省、城市的销售额数据。

因为是要同时显示省和城市两个维度，要制作两个图层，Rows里新加一个latitude(generated)，然后设置
<img src="https://img-blog.csdnimg.cn/7c03b30bef7b401fb846b6cd2ba496b1.png" width="400">


在mark里就会看到有2个图层
<img src="https://img-blog.csdnimg.cn/c6a0bd7069614198a23dec2c20f021cd.png" width="400">

设置第二个Marks，改成Circle，就可以显示出如果是Circle大就表明销售额数据高，并且颜色也深。
<img src="https://img-blog.csdnimg.cn/e018eef77a6845ab893f4aeed09b67cc.png" width="400">

如果是要制作热地图，则选择Density。
<img src="https://img-blog.csdnimg.cn/5410710e30d3486991dda1efb1be162d.png" width="400">

## 制作Dashboard
Dashboard是通过object组成的，一般是先选择一个Vertical的，然后再在Vertical中插入每一行，每一行就是要插入每一个Horizontal，然后再在Horizontal中插入每一个worksheet，并且一定要选择Tiled，否则如果选择Floating的话，worksheet是不能放进对应的Horizontal中。

<img src="https://img-blog.csdnimg.cn/3dbe8b47949a49d1b78834dad5909e16.png" width="400">

另外也可以在size里，自己custom size，选择range，可以指定最大和最小的size。

另外也可以参考别人比较好的dashboard，[https://www.vizwiz.com](https://www.vizwiz.com) 