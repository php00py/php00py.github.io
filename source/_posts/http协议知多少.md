---
title: http协议知多少
tags:
  - 网络知识
categories:
  - 网络知识
copyright: true
abbrlink: 4c2f67f
date: 2020-04-14 11:38:13
---
#  http简介

HTTP协议是Hyper Text Transfer Protocol（超文本传输协议）的缩写,是用于从万维网（WWW:World Wide Web
）服务器传输超文本到本地浏览器的传送协议。。

#  http工作原理

HTTP协议定义Web客户端如何从Web服务器请求Web页面，以及服务器如何把Web页面传送给客户端。HTTP协议采用了请求/响应模型。客户端向服务器发送一个请求报文，请求报文包含请求的方法、URL、协议版本、请求头部和请求数据。服务器以一个状态行作为响应，响应的内容包括协议的版本、成功或者错误代码、服务器信息、响应头部和响应数据。

  1. 客户端连接到Web服务器   
一个HTTP客户端，通常是浏览器，与Web服务器的HTTP端口（默认为80）建立一个TCP套接字连接。如www.rqlyy.top

  2. 发送HTTP请求   
通过TCP套接字，客户端向Web服务器发送一个文本的请求报文，一个请求报文由请求行、请求头部、空行和请求数据4部分组成。

  3. 服务器接受请求并返回HTTP响应   
Web服务器解析请求，定位请求资源。服务器将资源复本写到TCP套接字，由客户端读取。一个响应由状态行、响应头部、空行和响应数据4部分组成。

  4. 释放连接TCP连接   
若connection 模式为close，则服务器主动关闭TCP连接，客户端被动关闭连接，释放TCP连接;若connection
模式为keepalive，则该连接会保持一段时间，在该时间内可以继续接收请求;

  5. 客户端浏览器解析HTML内容   
客户端浏览器首先解析状态行，查看表明请求是否成功的状态代码。然后解析每一个响应头，响应头告知以下为若干字节的HTML文档和文档的字符集。客户端浏览器读取响应数据HTML，根据HTML的语法对其进行格式化，并在浏览器窗口中显示。

在游览器上输入URL(www.rqlyy.top)点击回车确认后。会经历以下过程：  
查找本地缓存或者本地host文件或者dns服务器将URL（www.rqlyy.top）解析成IP地址；  
解析的ip建立起tcp与服务器连接；  
浏览器发出读取文件(URL 中域名后面部分对应的文件)的HTTP 请求，该请求报文作为 TCP 三次握手的第三个报文的数据发送给服务器;  
服务器对浏览器请求作出响应，并把对应的 html 文本发送给浏览器;  
释放tcp连接  
游览器在解析html渲染显示在界面

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200414095130985.png?x-oss-
process=image0)  
CGI(Common Gateway Interface) 是 HTTP 服务器与你的或其它机器上的程序进行“交谈”的一种工具，其程序须运行在网络服务器上。

绝大多数的 CGI 程序被用来解释处理来自表单的输入信息，并在服务器产生相应的处理，或将相应的信息反馈给浏览器。CGI 程序使网页具有交互功能。

#  http结构

客户端发送一个HTTP请求到服务器的请求消息包括以下格式：请求行（request line）、请求头部（header）、空行和请求数据四个部分组成  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200414100237965.png)
HTTP响应也由四个部分组成，分别是：状态行、消息报头、空行和响应正文。  
![http响应](https://img-blog.csdnimg.cn/20200414101146174.png)

#  HTTP 请求方法

根据 HTTP 标准，HTTP 请求可以使用多种请求方法。

HTTP1.0 定义了三种请求方法： GET, POST 和 HEAD方法。  
HTTP1.1 新增了六种请求方法：OPTIONS、PUT、PATCH、DELETE、TRACE 和 CONNECT 方法。

请求方法  |  作用  
---|---  
GET  |  请求指定的页面信息，并返回实体主体  
POST  |  向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST
请求可能会导致新的资源的建立和/或已有资源的修改  
HEAD  |  类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头  
PUT  |  从客户端向服务器传送的数据取代指定的文档的内容  
DELETE  |  请求服务器删除指定的页面  
CONNECT  |  HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器  
OPTIONS  |  允许客户端查看服务器的性能  
TRACE  |  回显服务器收到的请求，主要用于测试或诊断  
PATCH  |  是对 PUT 方法的补充，用来对已知资源进行局部更新  
  
#  http请求头

每个头域由一个域名，冒号（:）和域值三部分组成。域名是大小写无关的，域值前可以添加任何数量的空格符，头域可以被扩展为多行，在每行开始处，使用至少一个空格或制表符。

Transport 头域

Connection：  
作用：表示是否需要持久连接。  
如果服务器看到这里的值为“Keep-Alive”，或者看到请求使用的是HTTP 1.1（HTTP
1.1默认进行持久连接）,它就可以利用持久连接的优点，当页面包含多个元素时（例如Applet，图片），显著地减少下载所需要的时间。要实现这一点
，服务器需要在应答中发送一个Content-Length头，最简单的实现方法是：先把内容写入
ByteArrayOutputStream，然后在正式写出内容之前计算它的大小；  
例如：　Connection: keep-alive
当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的TCP连接不会关闭，如果客户端再次访问这个服务器上的 网页，会继续使用这一条已经建立的连接  
例如：Connection: close 代表一个Request完成后，客户端和服务器之间用于传输HTTP数据的TCP连接会关闭，
当客户端再次发送Request，需要重新建立TCP连接。

Host:（发送请求时，该报文头域是必需的）  
Host请求报文头域主要用于指定被请求资源的Internet主机和端口号，它通常从HTTP URL中提取出来的。  
例如：http://；localhost/index.html  
浏览器发送的请求消息中，就会包含Host请求报头域，如下：  
Host：localhost  
此处使用缺省端口号80，若指定了端口号8080，则变成：Host：localhost:8080

Client 头域

Accept：  
作用：浏览器可以接受的媒体类型（MIME类型）,  
例如：Accept: text/html 代表浏览器可以接受服务器回发的类型为 text/html 也就是我们常说的html文档,
如果服务器无法返回text/html类型的数据，服务器应该返回一个406错误(non acceptable)。  
通配符 * 代表任意类型。例如 Accept: _/_ 代表浏览器可以处理所有类型，(一般浏览器发给服务器都是发这个)

Accept-Encoding：  
作用：浏览器申明自己接收的编码方法，通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate），（注意：这不是只字符编码）;  
例如：Accept-Encoding: gzip,
deflate。Server能够向支持gzip/deflate的浏览器返回经gzip或者deflate编码的HTML页面。许多情形下这可以减少5到10倍的下载时间，也节省带宽。

Accept-Language：  
作用：浏览器申明自己接收的语言。  
语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等；  
例如：Accept-Language:zh-cn 。如果请求消息中没有设置这个报头域，服务器假定客户端对各种语言都可以接受。

User-Agent：  
作用：告诉HTTP服务器， 客户端使用的操作系统和浏览器的名称和版本.  
我们上网登陆论坛的时候，往往会看到一些欢迎信息，其中列出了你的操作系统的名称和版本，你所使用的浏览器的名称和版本，这往往让很多人感到很神奇，实际上，
服务器应用程序就是从User-Agent这个请求报头域中获取到这些信息User-Agent请求报头域允许客户端将它的操作系统、浏览器和其它属性告诉服务器。  
例如：User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0;
CIBA; .NET CLR 2.0.50727; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729;
.NET4.0C; InfoPath.2; .NET4.0E)

Accept-Charset：  
作用：浏览器申明自己接收的字符集，这就是本文前面介绍的各种字符集和字符编码，如gb2312，utf-8（通常我们说Charset包括了相应的字符编码方案）；  
例如：Accept-Charset:iso-8859-1,gb2312.如果在请求消息中没有设置这个域，缺省是任何字符集都可以接受。

Authorization：授权信息，通常出现在对服务器发送的WWW-Authenticate头的应答中；  
Authorization请求报头域主要用于证明客户端有权查看某个资源。当浏览器访问一个页面时，如果收到服务器的响应代码为401（未授权），可以发送一个包含Authorization请求报头域的请求，要求服务器对其进行验证。

Cookie/Login 头域

Cookie:  
作用：最重要的header, 将cookie的值发送给HTTP 服务器

Entity头域

Content-Length  
作用：发送给HTTP服务器数据的长度。即请求消息正文的长度；  
例如：Content-Length: 38

Content-Type：  
作用：指定Body的类型。  
例如：Content-Type: application/x-www-form-urlencoded  
Content-Type主要有四类：  
1.text/html  
请求Web页面时返回响应的类型，Body中返回html文本。  
2.x-www-form-urlencoded  
Web页面纯文本表单的提交方式。  
3.multitype/form-data  
Web页面含有二进制文件时的提交方式。  
4.application/json,image/jpeg,application/zip…  
单项内容（文本或非文本都可以），用于Web Api的响应或者POST/PUT的请求。

Miscellaneous 头域

Referer:  
作用：提供了Request的上下文信息的服务器，告诉服务器我是从哪个链接过来的，比如从我主页上链接到一个朋友那里， 他的服务器就能够从HTTP
Referer中统计出每天有多少用户点击我主页上的链接访问他的网站。  
例如: Referer:http://translate.google.cn/?hl=zh-cn&tab=wT

Cache 头域

If-Modified-Since：  
作用：把浏览器端缓存页面的最后修改时间发送到服务器去，服务器会把这个时间与服务器上实际文件的最后修改时间进行对比。如果时间一致，那么返回304，客户端就直接使用本地缓存文件。如果时间不一致，就会返回200和新的文件内容。客户端接到之后，会丢弃旧文件，把新文件缓存起来，并显示在浏览器中。  
例如：If-Modified-Since: Thu, 09 Feb 2012 09:07:57 GMT。

If-None-Match：  
作用: If-None-Match和ETag一起工作，工作原理是在HTTP Response中添加ETag信息。当用户再次请求该资源时，将在HTTP
Request 中加入If-None-
Match信息(ETag的值)。如果服务器验证资源的ETag没有改变（该资源没有更新），将返回一个304状态告诉客户端使用本地缓存文件。否则将返回200状态和新的资源和Etag.
使用这样的机制将提高网站的性能  
例如: If-None-Match: “03f2b33c0bfcc1:0”

Pragma：  
作用：防止页面被缓存， 在HTTP/1.1版本中，它和Cache-Control:no-cache作用一模一样  
Pargma只有一个用法， 例如：Pragma: no-cache  
注意: 在HTTP/1.0版本中，只实现了Pragema:no-cache, 没有实现Cache-Control

Cache-Control：  
作用: 这个是非常重要的规则。这个用来指定Response-Request遵循的缓存机制。各个指令含义如下  
Cache-Control:Public 可以被任何缓存所缓存（）  
Cache-Control:Private 内容只缓存到私有缓存中  
Cache-Control:no-cache 所有内容都不会被缓存

#  http响应

http响应格式  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020041411311735.png)  
响应头部：

Date：  
作用：生成消息的具体时间和日期，即当前的GMT时间。  
例如：　Date: Sun, 17 Mar 2013 08:12:54 GMT

Expires：  
作用: 浏览器会在指定过期时间内使用本地缓存，指明应该在什么时候认为文档已经过期，从而不再缓存它。  
例如: Expires: Thu, 19 Nov 1981 08:52:00 GMT

Vary  
作用：指示不可缓存的请求头列表;  
例如: Vary: Accept-Encoding

Cookie/Login 头域

P3P  
作用: 用于跨域设置Cookie, 这样可以解决iframe跨域访问cookie的问题  
例如: P3P: CP=CURa ADMa DEVa PSAo PSDo OUR BUS UNI PUR INT DEM STA PRE COM NAV
OTC NOI DSP COR

Set-Cookie  
作用：非常重要的header, 用于把cookie 发送到客户端浏览器， 每一个写入cookie都会生成一个Set-Cookie.  
例如: Set-Cookie: PHPSESSID=c0huq7pdkmm5gg6osoe3mgjmm3;

path=/  
作用: 用于跨域设置Cookie, 这样可以解决iframe跨域访问cookie的问题  
例如: P3P: CP=CURa ADMa DEVa PSAo PSDo OUR BUS UNI PUR INT DEM STA PRE COM NAV
OTC NOI DSP COR

Set-Cookie  
作用：非常重要的header, 用于把cookie 发送到客户端浏览器， 每一个写入cookie都会生成一个Set-Cookie.  
例如: Set-Cookie: PHPSESSID=c0huq7pdkmm5gg6osoe3mgjmm3; path=/

Entity实体头域

实体内容的属性，包括实体信息类型，长度，压缩方法，最后一次修改时间，数据有效性等。

ETag：  
作用: 和If-None-Match 配合使用。（实例请看上节中If-None-Match的实例）  
例如: ETag: “03f2b33c0bfcc1:0”

Last-Modified：  
作用：用于指示资源的最后修改日期和时间。（实例请看上节的If-Modified-Since的实例）  
例如: Last-Modified: Wed, 21 Dec 2011 09:09:10 GMT

Content-Type：  
作用：WEB服务器告诉浏览器自己响应的对象的类型和字符集,  
例如:  
Content-Type: text/html; charset=utf-8  
Content-Type:text/html;charset=GB2312  
Content-Type: image/jpeg

Content-Length：  
指明实体正文的长度，以字节方式存储的十进制数字来表示。在数据下行的过程中，Content-
Length的方式要预先在服务器中缓存所有数据，然后所有数据再一股脑儿地发给客户端。  
例如: Content-Length: 19847

Content-Encoding：  
作用：文档的编码（Encode）方法。一般是压缩方式。  
WEB服务器表明自己使用了什么压缩方法（gzip，deflate）压缩响应中的对象。利用gzip压缩文档能够显著地减少HTML文档的下载时间。  
例如：Content-Encoding：gzip

Content-Language：  
作用：WEB服务器告诉浏览器自己响应的对象的语言者  
例如：Content-Language:da

Miscellaneous 头域

Server：  
作用：指明HTTP服务器的软件信息  
例如:Apache/2.2.8 (Win32) PHP/5.2.5

X-Powered-By：  
作用：表示网站是用什么技术开发的  
例如：X-Powered-By: PHP/5.2.5

Transport头域

Location：  
作用：用于重定向一个新的位置， 包含新的URL地址  
实例请看304状态实例

响应正文就是服务器返回的资源的内容，响应头和正文之间是一个回车和一个换行符隔开的。

#  http状态码

状态代码有三位数字组成，第一个数字定义了响应的类别，共分五种类别:

1xx：指示信息–表示请求已接收，继续处理

2xx：成功–表示请求已被成功接收、理解、接受

3xx：重定向–要完成请求必须进行更进一步的操作

4xx：客户端错误–请求有语法错误或请求无法实现

5xx：服务器端错误–服务器未能实现合法的请求

常见状态码：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200414113650242.png)

#  参考

https://www.runoob.com/http/  
https://www.cnblogs.com/an-wen/p/11180076.html  
https://www.cnblogs.com/qdhxhz/p/8468913.html

