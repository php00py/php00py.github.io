---
title: 文件上传upload-labs-maste
tags:
  - 文件上传
categories:
  - 文件上传
copyright: true
abbrlink: 4399f596
date: 2019-09-13 19:07:21
---
upload-labs-
maste是一个使用php语言编写的，专门收集渗透测试和CTF中遇到的各种上传漏洞的靶场。旨在帮助大家对上传漏洞有一个全面的了解。目前一共20关。  
源码下载地址为https://github.com/c0ny1/upload-
labs，我搭建的环境为phpstudy+window10环境，将下载的源码放到根目录下的www目录下，然后访问http://127.0.0.1
/upload-labs-master/，如图所示即环境搭建完成，可以进行闯关练习。  
上传检测的5步基本流程  
1.客户端javascript检测（检测文件扩展名为主）  
2.服务端MIME类型检测（检测Content-type内容）  
3.服务端目录路劲检测（检测跟path相关的内容）  
4.服务端文件扩展名检测（检测跟文件后缀相关的内容）  
5.服务端文件内容检测（检测内容是否合法或含有恶意代码）  
客户端Javascript检测：  
通常在js文件中有一个检测的函数，一般对上传的文件后缀名做检测，比如说仅允许上传png,jpg,gif类型的文件，如果检测到的文件后缀名不是在这些白名单内，则不向服务器端传输上传文件的内容。  
这类检测可以使用firebug之类的插件把它禁掉或者通过burp之类的代理工具进行改后缀名提交。或者直接F12将检测函数去掉。即可绕过js检测。文件上传通常还配合解析漏洞结合使用。
![W](https://img-blog.csdnimg.cn/20190913174919450.png)
下图是文件上传绕过的总结。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913175147488.png)
第一关：客户端JavaScript验证，上传一个8.php.jpg文件，然后通过代理burip suit抓包，去掉jpg后缀。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913175605278.png)  
上传成功后，访问图片。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913175814486.png)  
第二关：上传9.php，提示文件类型不正确，请重新上传，说明在服务端做文件类型验证。如图  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913180455514.png)  
抓包，修改服务端对数据包的MIME进行检查。将Content_Type：application/octent-stream,修改为Content-
Type:image/jpeng.  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913181204350.png)  
成功上传。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913181227233.png)  
第三关：上传9.php文件，提示不允许上传aspphp后缀文件，如图，尝试对后缀名大小写绕过。如大小写phtml|php5|.php4|.php3|.php2|php1|。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913181410526.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913181644368.png)  
第四关：上传9.php文件，提示此文件不允许上传，服务器黑名单验证，  
几乎过滤了所有有问题的后缀名。但是没有过滤.htaccess,先上传一.htaccess文件，它的内容为：  
SetHandler application/x-httpd-php，将所有文件都当作PHP解析。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019091318182840.png)  
然后在上传一个8.jpg木马文件  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019091318203027.png)  
上传成功。访问图片地址。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913182104770.png)  
第五关：上传9.php文件，提示此文件类型不允许上传，还是在服务器黑名单中，.htaccess在黑名单内。此时可尝试大小写绕过。如图，将9.php改为9.PHP  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913182231426.png)  
成功上传，访问图片位置 。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019091318234367.png)  
第六关：上传9.php文件，但是提示此文件不允许上传，服务器黑名单检测。通过加空格绕过，在9.php后面加个空格成功上传文件.  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913182440188.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913182502305.png)  
第七关：上传php文件，还是黑名单，利用Windows特性，在后缀名中加.绕过。在9.php后面加上点  
。 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913182654774.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913182704468.png)  
第八关：黑名单，后缀加::  D  A  T  A  绕  过  ，  在  9\.  p  h  p  后  面  加  上  :  :
DATA绕过，在9.php后面加上::  D  A  T  A  绕  过  ，  在  9  .  p  h  p  后  面  加  上  :  :
DATA  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913183256401.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913183309434.png)

第九关：空格黑名单… . 后缀名…空格.绕过  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913183343191.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913183404217.png)  
第10关：上传9.php,发现后缀会去掉  
双写去绕过  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913183428727.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913183435203.png)

第十一关：特别注意%00截断前提下为版本小于5.3.29并且magic_quotes_gpc = Off  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019091319021017.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913190220203.png)  
第十二关：同样是00截断。将%00进行urlencode编码  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913190257820.png)  
第十三关，上传图片码，copy 666.jpg/b websell.php/b shell.jpg  
上传成功图片码后。利用文件包含绕过。  
创建简单的文件包含文件代码如下：

<?php $file = $_GET[“page”]; Include($file);?>

上传图片码，然后利用文件包含。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190913190533411.png)

