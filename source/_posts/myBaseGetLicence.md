---
title: 完美解决myBase Desktop 破解
categories: tools
tags: tools
abbrlink: a0885743
date: 2022-01-10 16:54:14
---
不得不说myBase是非常好的一款工具，几乎用过所有的windows下的笔记软件，不同于那些将数据同步到云端的笔记软件，myBase 特别适合本地电脑的使用，特别适合于工作，鉴于myBase要求付费，现在介绍一种方法不用付费

<!--more-->

## 参考资料
http://jingyan.baidu.com/article/d8072ac47f5a02ec95cefdbd.html

## 步骤
上面链接中说的不适合于在新版的myBase软件中更改，新版中的nyfedit.ini文件，已经修改为myBase.ini，并且如果按照python, time.time() 修改Lic.FirstUseOn属性的话，发现再次打开软件，还是没有效果，   所以直接干脆删除这个属性，再次打开发现解决问题。但是有的时候需要修改成time.time()得出的值。

今天周末闲来无事，写了个小脚本，注意用法：
将下面的code，保存成 yourfilename.py,然后放到对应的有myBase.ini文件目录下，执行python  yourfilename.py 即可
```
__author__ = 'haofan'
import time
import os
filename = "myBaseTest.ini"
print filename
insertNewline = "App.UserLic.FirstUseOn=" + str(int(time.time())) + "\n"
print insertNewline
with open(filename, 'r') as f:
    lines = f.readlines()
    lines[100] = insertNewline
with open(filename, 'w') as f:
   f.writelines(lines)
   f.close()
```

