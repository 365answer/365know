---
title: SSH Host key verification failed
tags: [工具, ssh]
---

# SSH Host key verification failed

## 场景

一台Ubuntu 20.04的主机，安装了openssh-server，IP地址是192.168.100.185。

Windows系统中，使用Windows Terminal的ssh指令连接Ubuntu主机，出现如下报错：

```bash
PS C:\Users\Administrator> ssh linux@192.168.100.185
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:A/ywFpXZgLH0RewCEA8ngbg5U7kOcZFATq4yDRRoV4w.
Please contact your system administrator.
Add correct host key in C:\\Users\\Administrator/.ssh/known_hosts to get rid of this message.
ECDSA host key for 192.168.100.185 has changed and you have requested strict checking.
Host key verification failed.
```



## 分析

之前使用ssh连接过另一台Ubuntu主机，IP地址恰好也是192.168.100.185，所以ssh就保存了一份host key。所以，当使用之前保存的host key来连接当前这台Ubuntu20.04主机时，出现了Host key verification failed。说白了，就是拿旧钥匙开新锁，当然是打不开。



## 解决方法

打开C:\\Users\\Administrator/.ssh/known_hosts文件，删除IP地址192.168.100.185相关的信息，例如：

```bash
192.168.100.185 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYBBBBIbmlzdHAyNTYAAABBBKAl/fo3zb1243t9JklLLqWaal2NRInKG9NPFP3AKAW0R+R6W4ylAF+TunJZt+QtnfZCxxpcZe4ErQcBo0sd9+Q=
```

删除之后，重新连接即可。