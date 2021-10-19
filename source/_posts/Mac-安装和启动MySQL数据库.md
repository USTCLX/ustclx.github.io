---
title: Mac 安装和启动MySQL数据库
date: 2021-10-19 11:05:29
tags:
  - MySQL
---

最近在学习 NestJS 框架，需要使用 MySQL 数据库来配合完成一些功能，以前只使用过 MongoDB，第一次使用 MySQL，在安装和使用时踩了一些坑，记录下来。

## 下载

MySQL 分为商业版和社区版，社区版我理解就是开源免费的。但是在官网想要找到下载入口比较费劲，个人感觉不是清晰。下载社区版可以直接使用下面这个链接。
[MySQL Community Download](https://dev.mysql.com/downloads/mysql/)

## 安装

下载完成后，直接双击 dmg 文件进行安装。安装过程中需要输入输入 root 的密码，这里需要记下来。后面链接数据库的时候需要使用。

![mysql_install](/images/mysql_install.png)

## 启动

为了启动 MySQL 我走了不少弯路，一开始使用命令行来启动，结果很麻烦，也没有成功启动。后来发现其实 MySQL 很人性化，它会在 System Perference 里面生成一个图标，点击图标后就可进行一些基本操作，包括

- start
- stop
- initialize

![mysql_perference_1](/images/mysql_perference_1.png)
![mysql_perference_2](/images/mysql_perference_2.png)

## 连接数据库

建议直接使用图形化界面来操作，使用命令行太麻烦了。图形化界面有两个选择一个是收费的`Navicat`，一个是开源免费的`Sequel Pro`。
笔者一开始试用过`Navicat`，确实非常好用，鉴于平时需求不是很大，所以暂时没有打算付费使用。于是选择了`Sequel Pro`。

在使用`Sequel Pro`的过程中遇到了一些问题。最开始下载的是`v1.1.2`正式版，但是使用过程中无法连接数据库，还总是闪退。尝试重装也没有解决。搜索后发现很多人都遇到了这个问题。解决方案是下载`test build`版本。可以完美解决。

下载地址: [Sequel Pro test build](https://sequelpro.com/test-builds)

![sequel_pro_1](/images/sequel_pro_1.png)
![sequel_pro_2](/images/sequel_pro_2.png)

如此依赖就可以快乐的使用 MySQL 数据库了。
