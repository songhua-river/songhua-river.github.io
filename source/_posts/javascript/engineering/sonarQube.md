---
title: sonarQube
mathjax: true
categories:
  - JavaScript
  - 工程化
tags:
  - 工程化
abbrlink: f6475fcf
date: 2020-12-04 16:40:25
---

#### 下载 sonarQube

![](0001.png)

#### Java 环境

ubuntu 版本

![](0002.png)

![](0003.png)



服务端： 规则放在服务端，提供一些web hook 和 Jenkins 集成

客户端：在客户端执行，首先去服务端拉取规则 ☝ ，根据规则扫描代码，在本地生成报表， 把报表上传到服务器 ☝ 。