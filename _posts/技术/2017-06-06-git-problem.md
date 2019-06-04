---
layout: post
title: git经验贴
description: git经验贴
category: 技术
---

---
layout: post
title: git经验贴
description: git经验贴
category: 技术
---

## 1.在Ubuntu下使用$ git clone时出现server certificate verification failed. CAfile:/etc/ssl/certs/ca-certificates.crt CRLfile: none

> 解决方法：在执行$ git clone 之前，在终端输入：
export GIT_SSL_NO_VERIFY=1
  
## 2.在vpn环境下，使用git下载https项目时，出现了GnuTLS recv error (-9): A TLS packet with unexpected length was received

>解决方法：
ifconfig eth0 mtu 1400
GIT_CURL_VERBOSE=1
apt-get install stunnel4

## 3.从 github 执行 git clone 一个大的项目时提示 error: RPC failed

>解决方法：git config http.postBuffer 524288000
To set this gloablly for all remote Git repositories you ever connect to
或者
git config --global http.postBuffer 524288000
另外低版本的git也可能存在此问题，
ubuntu下git升级方法如下：
add-apt-repository ppa:git-core/ppaapt-get updateapt-get dist-upgrade

## 4. git clone 出现fatal: unable to access SSL certificate problem: unable to get local issuer certificate

>解决方法：git config --global http.sslVerify false
