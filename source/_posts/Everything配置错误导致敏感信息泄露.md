---
title: Everything配置错误导致敏感信息泄露
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: c2c2914
date: 2019-10-16 20:52:11
---
**1.Everything介绍**  
Everything是一个私有的免费Windows桌面搜索引擎，可以在NTFS卷上快速地根据名称查找文件和目录。  
“Everything” 是 Windows 上一款搜索引擎，它能够基于文件名快速定文件和文件夹位置。  
不像 Windows 内置搜索，“Everything” 默认显示电脑上每个文件和文件夹 (就如其名 “Everything”)。

**2.漏洞描述**  
由于在配置中开启了ETP/FTP服务和HTTP服务，导致可以直接访问服务器上的文件。从而任意下载服务器上的文件。

**3.漏洞复现**  
官网下载最新版本  
https://everything.en.softonic.com  
打开Everything，在 工具 – 选项 – HTTP服务器处，启动HTTP服务并设置端口，在这里我设置为8888  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191016204315768.png)  
web访问本地端口的8888即可看到本地文件信息  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191016204424397.png)
谷歌搜索公网上的everything  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191016205011861.png)
**4.修复**  
最好是关闭http服务功能，或者添加服务器用户名和密码

