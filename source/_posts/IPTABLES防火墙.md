---
title: Linux IPTABLE防火墙
date: 2017-04-24 19:56:24
tags:
- Linux
- 防火情 
- iptables
---
### AB 压力测试

向目标主机(tabao.com)发送十万次请求，每次并发100

```
ab -n 100000 -n 100 http://www.taobao.com/
```

### IPTABLES
#### ipatbles 的四表
1. filter表
2. nat表
3. mangle表
4. raw表

#### iptables 的五链(HOOK POINT)
1. INPUT
2. OUTPUT
3. FORWARD
4. PREROUTING
5. POSTROUTING

#### iptables 规则组成
1. 数据包访问控制 ACCEPT DROP REJECT
2. 数据包改写  SNAT（源地址改写） DNAT（目标地址改写）
3. 信息记录 LOG

#### iptables 命令

iptables|table|command|chain|Parameter & Xmatch|target
---|---|---|---|---|---|
iptables|-t filter<br> nat| -A(追加规则)<br> -D(删除规则)<br> -L(显示)<br> -F(清理规则)<br> -P(默认规则)<br> -I（在首位添加规则)<br> -R<br> -n|INPUT<br> FORWARD<br> OUTPUT<br> PREROUTING<br> POSTROUTING<br> | -p tcp<br> -s<br> -d<br> --sport<br> --dport<br> --dports<br> -m tpc<br> state multiport| -j ACCEPT<br> DROP<br> REJECT<br> DNAT<br> SANT


#### iptable 配置场景一
1. 对所有的地址开放本季的tcp(80, 22, 10-21)的端口的访问
2. 允许对所有的地址开放本机的基于ICMP协议的数据包访问
3. 其他未被允许的端口规则禁止访问

查看iptables的版本

```
vagrant@ubuntu:~$ iptables -v
iptables v1.4.21: no command specified
Try `iptables -h' or 'iptables --help' for more information.
```

列出当情的防火墙配置

```
root@ubuntu:~# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy DROP)
target     prot opt source               destination
DOCKER-ISOLATION  all  --  anywhere             anywhere
DOCKER     all  --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
ACCEPT     all  --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

Chain DOCKER (1 references)
target     prot opt source               destination
ACCEPT     tcp  --  anywhere             172.17.0.3           tcp dpt:mysql
ACCEPT     tcp  --  anywhere             172.17.0.2           tcp dpt:3000
ACCEPT     tcp  --  anywhere             172.17.0.2           tcp dpt:ssh

Chain DOCKER-ISOLATION (1 references)
target     prot opt source               destination
RETURN     all  --  anywhere             anywhere
root@ubuntu:~# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy DROP)
target     prot opt source               destination
DOCKER-ISOLATION  all  --  0.0.0.0/0            0.0.0.0/0
DOCKER     all  --  0.0.0.0/0            0.0.0.0/0
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

Chain DOCKER (1 references)
target     prot opt source               destination
ACCEPT     tcp  --  0.0.0.0/0            172.17.0.3           tcp dpt:3306
ACCEPT     tcp  --  0.0.0.0/0            172.17.0.2           tcp dpt:3000
ACCEPT     tcp  --  0.0.0.0/0            172.17.0.2           tcp dpt:22

Chain DOCKER-ISOLATION (1 references)
target     prot opt source               destination
RETURN     all  --  0.0.0.0/0            0.0.0.0/0
```
清除配置表

```
root@ubuntu:~# iptables -F
root@ubuntu:~# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
root@ubuntu:~#
```
配置防火墙

```
iptables -I INPUT -p tcp --dport 80 -j ACCEPT
iptables -I INPUT -p tcp --dport 22 -j ACCEPT
iptables -I INPUT -p tcp --dport 10:21 -j ACCEPT
iptables -I INPUT -p icmp -j ACCEPT
```

```
root@ubuntu:~# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpts:10:21
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```
拒绝其他的访问请求
```
iptables -A INPUT -j REJECT
```

```
root@ubuntu:~# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpts:10:21
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80
REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-port-unreachable

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

删除之前80端口的防火墙配置

```
iptables -D INPUT -p tcp --dport 80 -j ACCEPT
```

```
root@ubuntu:~# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpts:10:21
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22
REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-port-unreachable

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

禁止访问80端口
```
iptables -I INPUT -p tcp --dport 80 -j REJECT
```

```
root@ubuntu:~# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80 reject-with icmp-port-unreachable
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpts:10:21
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22
REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-port-unreachable

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

场景存在的问题

- 本机无法访问本机 
- 本机无妨访问其他主机

允许所有通过lo设备的数据包

```
iptables -I INPUT -i lo -j ACCEPT
```

```
root@ubuntu:~# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --   /0            0.0.0.0/0
REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80 reject-with icmp-port-unreachable
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpts:10:21
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22
REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-port-unreachable

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```
对处于established和related状态下，放行数据

```
iptables -I INPUT -m state --state 	ESTABLISHED,RELATED -j ACCEPT
```
```
root@ubuntu:~# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
ACCEPT     all  --  anywhere             anywhere
REJECT     tcp  --  anywhere             anywhere             tcp dpt:http reject-with icmp-port-unreachable
ACCEPT     tcp  --  anywhere             anywhere             tcp dpts:10:ftp
ACCEPT     icmp --  anywhere             anywhere
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
REJECT     all  --  anywhere             anywhere             reject-with icmp-port-unreachable

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

只允许特定的源地址访问80端口

删除之前的iptables规则

```
iptables -D INPUT -p tcp --dport 80 -j REJECT
```

```
root@ubuntu:~# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
ACCEPT     all  --  anywhere             anywhere
ACCEPT     tcp  --  anywhere             anywhere             tcp dpts:10:ftp
ACCEPT     icmp --  anywhere             anywhere
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
REJECT     all  --  anywhere             anywhere             reject-with icmp-port-unreachable

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```
只允许10.10.188.233访问80端口

```
iptables -I INPUT -p tcp -s 10.10.188.233 --dport 80 -j ACCEPT
```

```
root@ubuntu:~# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     tcp  --  10.10.188.233        anywhere             tcp dpt:http
ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
ACCEPT     all  --  anywhere             anywhere
ACCEPT     tcp  --  anywhere             anywhere             tcp dpts:10:ftp
ACCEPT     icmp --  anywhere             anywhere
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
REJECT     all  --  anywhere             anywhere             reject-with icmp-port-unreachable

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```
