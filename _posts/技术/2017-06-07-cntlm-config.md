---
layout: post
title: cntlm配置指导
description: cntlm配置指导
category: 技术
---

## 初始化文件中主要有三个地方需要设置
```
Username	username
Domain		domain
Auth            NTLM
PassNT          8010B2C82D05390DDC69C8D80B4B2C25
PassLM          437C8FD7782CC00C3306EAA1C1824BAC
Proxy		proxy.xx.com:8080

NoProxy		localhost, 127.0.0.*, 10.*, 192.168.*


Listen		0.0.0.0:59998
```


## 配置NTLM认证信息

 在命令行下执行：
> cntlm.exe -c cntlm.ini -v -I -f -M http://www.baidu.com，

并按提示输入域密码，屏幕 将会输出很多信息，注意其中类似如下的信息，一般在最后。
```
－－－－－－－－－－－－－－－－－－－[ Profile　　1 ]－－－  
Auth　　　　　　　　　NTLM  
PassNT　　　　　　　　27BB47C50B2F6A020DB32B2770469D25  
PassLM　　　　　　　　EF063A95CC529D14CD8F033A09E52B23  
－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－
```
然后将两条分隔线中间的部分拷贝到cntlm.ini中，在执行开始菜单中的“Start Cntlm”。

