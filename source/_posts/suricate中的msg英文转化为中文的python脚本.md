---
title: suricate中的msg转化为中文的Python脚本
categories: Python
tags:
  - Python
  - suricate
date: 2021-04-21 10:28:32
abbrlink: 9e558de9

---


# 将suricata中的msg英文转化为中文的python脚本


**前提**  
因工作需要，需要对suricata更新中的msg字段中的英文转化为中文。

**#excel的文档**  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d09fff978b127d42b104d41f66db29e9.png)**python脚本**

```
# -*- coding:utf-8 -*-

#'msg: "(.*?)"|msg:"(.*?)";'
#ms = "".join(msg[0])
#该脚本是将英文的msg转化为中文的msg
import re 
sum = 0
zhmsg =[]
with open("1.txt",'r',encoding='utf-8')as fr:
    for i in fr.readlines():
        i = i.strip("\n")
        zhmsg.append(i)

with open("nocvesuricata.rules",'r',encoding="utf-8")as rules:
    for i in rules.readlines():
        #print(i)
        #print(i)
        #print(sum)
        ms = re.findall('msg: "(.*?)"|msg:"(.*?)";',i)
        enmsg = "".join(ms[0])
        msg =zhmsg[sum]
        i = i.replace(enmsg,msg)
        with open("zhmsgAttack.rules",'a',encoding="utf-8")as zmsg:
            zmsg.write(i+"\n")
        print(i)
        sum += 1
        
print("总个数：",zhmsg)
```

**效果**  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/49d3db37ee42f6aec48f8b59d002fe44.png)![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d383fa495b23335f318444d48a9d6d82.png)

![](https://csdnimg.cn/release/blogv2/dist/pc/img/vip-limited-close-newWhite.png)
