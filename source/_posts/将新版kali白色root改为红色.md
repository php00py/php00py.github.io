---
title: 将新版kali白色root改为红色
tags:
  - 网络知识
categories:
  - 网络知识
copyright: true
abbrlink: 7c4a0fe1
date: 2020-03-10 16:17:42
---
今天更新了kali2020.1，发现root终端的颜色是白色，初始用户不在是root，而是变成了kali  
登录root用户，发现终端的颜色是白的，不是红色的。用着很不习惯。如图所示  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200310161052924.png) cp

cp /home/kali/.bashrc /root  
将kali目录下的.bashrc复制到root目录下  
然后替换终端颜色  
source .bashrc

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020031016171760.png)

