---
title: 一款攻击检测工具suricata
tags:
  - 安全攻防
categories:
  - 安全攻防
copyright: true
abbrlink: ea118df4
date: 2020-03-06 09:50:48
---
**0x01 suricata介绍**  
Suricata是一个高性能的IDS、IPS和网络安全监控的引擎。它是开源的，由一个社区经营的非营利基金会开放信息安全基金会（OISF）开发。  
https://www.osgeo.cn/suricata/what-is-suricata.html

**0x02 suricata语法**  
规则语法由规则头部和规则选项组成  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200306093702566.png)  
alert tcp $EXTERNAL_NET $FILE_DATA_PORTS -> $HOME_NET any（ rule options ）  
规则行为 协议 源ip 源端口 流量方向 目标ip 目标端口 规则选项  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200306094838491.png)
语法：action：确认特征/签名匹配会发生什么  
Header: 协议、ip地址、端口和规则的方向  
Rule options:定义规则细节

Action：默认顺序为：pass，drop，reject，alert  
Pass :匹配到规则后，suricata停止扫描数据包，并跳到所有规则末尾(仅针对当前数据包)  
Drop: ips模式使用，如果匹配到，则立即阻断数据包不会发送任何信息。  
Reject :对数据包主动拒绝，接收者与发送者都会收到一个拒绝包。  
Alert：记录所有匹配规则并记录与匹配规则相关的数据包并产生报警

协议：在规则中指定匹配那些协议。  
源ip和目的ip:分别指定流量的来源和目标  
源端口和目的端口：指定流量流入流出的端口

流量的方向：-> 从源ip到目标的ip单向流量  
<> 双向流量，2个ip往返之间的流量

特征标识符sid：用于唯一性规则标识，sid不能重复。而且为数值。  
a. 0-10000000：为Sourcefire VRT保留  
b. 20000000-29999999：为 Emerging Threats（ET）保留  
c. 30000000+ ：公用

修订（rev）：用于表示规则发生了修改，可以理解为规则的版本号  
a. 当创建一条新规则时，制定 rev:1; ，以表明该规则为第一版本  
b. 当规则被改变时，无需创建新规则，可保持sid不变，使rev递增

引用（reference）：用于链接外部信息来源，从而为规则提供附加的情景资料  
好处：保持规则整洁，减小规则长度，使其更易编辑管理

    
    
    a. 直接指定： reference:<reference type>,<reference>;
                reference:url,doc.emergingthreats.net/2010235
    
    b. 可通过在suricata.yaml配置reference.config文件来定义引用类型
       文件中使用格式：config reference:<reference type><reference prefix>;
       例如  文件中：config reference:cve http://cve.mitre.org/cgi-bin/cvename.cgi?name=
            规则中：reference:cve,2001-0414
            实际引用：reference:cve,http://cve.mitre.org/cgi-bin/cvename.cgi?name=2001-0414
    

优先级（priority）：用于手动指定规则的优先级，提升分析员查看警报的效率  
此选项可以任意整数设置，可使用0-10之间的数指定优先级，0最高，10最低

类别（classtype）：用于根据规则所检测的活动类型为规则分类  
规则中指定方式：classtype:;  
通过在suricata.yaml配置classification .config文件来描述规则中的类别

文件中格式：config classification: ,,  
classification name：类别名称  
classification description：类别描述  
classification privoroty：类别指定的默认优先级，当规则使用类别时以此处优先级为准

**0x03 检测内容**  
1.检查内容（content）：检查数据包内容中是否包含某个字符串  
如：content:“evilliveshere”;  
指定多个匹配项：content:“evilliveshere”; content:“here”;  
a. 使用感叹号！对匹配项的否定：content:!“evilliveshere”;  
b. 将字符串的十六进制用管道符（|）进行包围：content:"|FF D8|";  
c. 字符串与十六进制混合使用：content:"|FF D8|evilliveshere";  
d. 匹配内容区分大小写  
e. 保留字符（; \ "）须进行转义或十六进制转码

  2. 检测内容修饰语：通过在匹配内容之后添加一些修饰语，可以精确控制IDS引擎在网络数据中匹配内容的方式。 
    
         a. nocase：匹配内容不区分大小写，如 content:"root";nocase;
    
     b. offset：用于表示从数据包载荷的特定位置开始内容匹配，从载荷其实位置算起
                注意载荷开始位置从0字节处开始，而不是1字节处
                content:"root";offset:5;
    
     c. depth：用于限制搜索匹配内容的结束位置。若使用了offset，则开始位置为offset，否则为载荷开始位置
               content:"root";offset:5;depth:7;
    
     d. distance：用于指定上一次内容匹配的结束位置距离本次内容匹配的开始位置的距离
    
     e. within：用于限制本次匹配必须出现在上一次匹配内容结束后的多少个字节之内
    
     f. distance和within的同时使用限制了第二次内容匹配的匹配范围，如下
        content:"evilliveshere";  content:"here"; distance:1;within:7;
        在匹配字符串“evilliveshere”后的1到7个字节范围内对字符串“here”进行匹配
    
     g. http内容修饰语：针对检测http流量的规则，Suricata提供了http流重组能力，同时提供了用于编写HTTP流量相关的更高效的规则修饰器
        例如：alert tcp any any -> any 80(msg:"Evil Doamin www.appliednsm.com"; "content:"GET";httpmethod;  content:"www.appliednsm.com";http_uri; sid:5445555; rev:1;)
    
     常用http内容修饰语：
     http_client_body       HTTP客户端请求的主体内容
     http_cookie         HTTP头字段的“Cookie”内容
     http_header         HTTP请求或响应头的任何内容
     http_method         客户端使用的HTTP方法（GET，POST等）
     http_uri               HTTP客户端请求的URI内容
     http_stat_code       服务器响应的HTTP状态字段内容
     http_stat_message     服务器响应的HTTP状态消息内容
     http_encode         在HTTP传输过程中所使用的编码类型
    
    
        3. 兼容Perl语法的正则表达式（PCRE）：通过正则表达式对内容进行匹配
       匹配信用卡号码： pcre:"/([0-6]\d\d|7[0-256]\d|73[0-3]|77[0-2])-\d{2} - \d{4}  /";      
    
    

  3. 通信流量   
通信流量：根据TCP协议网络流量状态编写规则  
三次握手：  
1\. 客户端向服务器的监听端口发送SYN包来发起连接  
2\. 此时，服务器需要向客户端回复一个SYN/ACK包  
3\. 一旦收到该包，客户端会回复给服务器一个ACK包

数据流规则有一些专用选项，分为三类：  
1\. 状态选项  
2\. 定向选项  
3\. 流量模式状态

配置格式：flow:,,

有效声明选项分别为  
1\. established：只匹配已经建立TCP会话连接的流量  
2\. stateless：不论是否已建立TCP会话连接均匹配

方向选项分别为：  
1\. to_server：从客户端到服务端的流量  
2\. from_server：从服务端到客户端的流量  
3\. to_client：从服务端到客户端的流量  
4\. from _client：从客户端到服务端的流量

no_stream：待匹配数据是重组后的流  
only_stream：待匹配数据是单独的数据包

协议头检测  
1\. TTL：匹配指定的TTL指，可使用关系运算符（< , <=, >=, >）,可以用来识别操作系统类型  
2\. dsize：匹配一个指定payload大小的数据包，可使用关系运算符（< , <=, >=, >）  
3\. itype：匹配指定的ICMP类型值  
4\. icode：匹配指定的ICMP代码值  
5\. ip_proto：匹配指定的IP协议，如IGMP、GRE

