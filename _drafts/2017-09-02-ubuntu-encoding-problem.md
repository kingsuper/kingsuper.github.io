---
layout: post
title: MySQL ubuntu 乱码解决 
description: ubuntu 乱码解决 
category: 技术
---

```
sudo apt-get install convmv
convmv -r -f cp936 -t utf8 --notest --nosmart *
```

```
apt-get install enca
enconv -L zh_CN -x UTF-8 *
```
