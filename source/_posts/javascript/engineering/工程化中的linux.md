---
title: 工程化中的linux
mathjax: true
abbrlink: ed6b4dad
date: 2020-12-02 16:52:11
categories:
  - JavaScript
  - 工程化
tags:
  - 工程化
---

#### 免密登陆

+ 生成密钥对

```bash
ssh-keygen -t rsa -C "自己的名字" -f(自定义文件名) "名字_rsa"
```

+ 上传共钥到服务器对应账号的home目录下.ssh文件夹下面

公钥的权限为600

```bash
ssh-copy-id -i mykey_rsa.pub user@xxx.xxx.xxx.xxx 
```

+ 指定私钥登陆

私钥的权限为600

```bash
ssh -i 私钥 user@xxx.xxx.xxx.xxx
```

+ 通过配置文件免密登陆

把私钥复制到home下的.ssh文件夹下面

创建cnofig文件

配置单一服务器免密登陆

```bash
Host 别名
　　HostName IP
　　Port 端口
　　User 用户名
　　IdentitiesOnly yes
   IdentityFile ~/.ssh/user_rsa  (私钥路径)
   Protocal 2 (协议版本号)
   Compression yes 
   ServerAliveInterval 60 （防止被踢配置，长时间没有操作会被踢掉，每隔60秒发一个信号）
   ServerAliveCountMax 20 (最大连接数)
   LogLevel INFO
```

多个账号的免密的登陆

分别配置部分

```bash
Host xxx-produce
HostName IP
Port 端口
```

公共配置部分

```bash
Host *_produce
其他配置相同
```

config的权限为 644