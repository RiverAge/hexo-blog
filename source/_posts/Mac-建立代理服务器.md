---
title: Mac 建立代理服务器
date: 2017-03-05 18:37:21
tags:
- Privoxy
- 代理服务器
- 翻墙
---

手机客户端服务器在设置代理服务器之后，手机上的所有通信都会经过制定的代理服务器。这样的话，如果代理服务器如果通过 VPN 或 SS 连接到外网络的话，连接到代理服务器的手机也将会访问到外网。这样做的话我们手机的客户端除了指定服务器地址的话，不需要其他的配置就可以直接翻墙了。

首先在 Mac 上建立代理服务器

```
brew install Privoxy
```

编辑配置文件

```
echo 'listen-address' 0.0.0.0:9092' >> /usr/local/etc/privoxy/config
```

启动 Privoxy

```
/usr/local/Cellar/privoxy/3.0.24/sbin/privoxy /usr/local/etc/privoxy/config
```

检查 Privoxy 进程

```
ps aux | grep privoxy
euky             69148   0.0  0.0  2461160   1068   ??  Ss    6:35下午   0:00.05 /usr/local/Cellar/privoxy/3.0.24/sbin/privoxy /usr/local/etc/privoxy/config
euky             69483   0.0  0.0  2440996    704 s003  R+    6:48下午   0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn privoxy

```

这样 Privoxy 已经提供了监听在9092端口的 HTTP 代理服务器。

开启记录日志

```
echo 'debug 1' >> /usr/local/etc/privoxy/config
tail -f /usr/local/var/log/privoxy/logfile
```

