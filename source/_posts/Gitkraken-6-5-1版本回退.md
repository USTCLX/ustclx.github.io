---
title: Gitkraken 6.5.1版本回退
date: 2022-04-24 16:35:46
tags:
  - 奇奇怪怪的问题的解决方案
---

Gitkraken 是一款 Git 的图形化操作工作，个人很喜欢他的 UI 和操作方式。但是最新版的 Gitkraken 要求必须付费才能使用和操作私有仓库。6.5.1 是支持免费私有仓库的最后一个版本。有需要的朋友可以自己选择下载。有条件的朋友也可以支持一下付费最新版。

PS: Gitkraken 有自动更新的功能，如果需要关闭，在不同的操作系统上有不同的方法。不过，通过修改 host 文件，可以一劳永逸。

### Gitkraken 6.5.1 官方地址

- win-x86:

[release.axocdn.com/win32/GitKrakenSetup-6.5.1.exe](release.axocdn.com/win32/GitKrakenSetup-6.5.1.exe)

[release.gitkraken.com/win32/GitKrakenSetup-6.5.1.exe](release.gitkraken.com/win32/GitKrakenSetup-6.5.1.exe)

- win-x64:

[release.axocdn.com/win64/GitKrakenSetup-6.5.1.exe](release.axocdn.com/win64/GitKrakenSetup-6.5.1.exe)

[release.gitkraken.com/win64/GitKrakenSetup-6.5.1.exe](release.gitkraken.com/win64/GitKrakenSetup-6.5.1.exe)

- linux-deb:

[release.axocdn.com/linux/GitKraken-v6.5.1.deb](release.axocdn.com/linux/GitKraken-v6.5.1.deb)

[release.gitkraken.com/linux/GitKraken-v6.5.1.deb](release.gitkraken.com/linux/GitKraken-v6.5.1.deb)

- linux-rpm:

[release.axocdn.com/linux/GitKraken-v6.5.1.rpm](release.axocdn.com/linux/GitKraken-v6.5.1.rpm)

[release.gitkraken.com/linux/GitKraken-v6.5.1.rpm](release.gitkraken.com/linux/GitKraken-v6.5.1.rpm)

- linux-tar.gz:

[release.axocdn.com/linux/GitKraken-v6.5.1.tar.gz](release.axocdn.com/linux/GitKraken-v6.5.1.tar.gz)

[release.gitkraken.com/linux/GitKraken-v6.5.1.tar.gz](release.gitkraken.com/linux/GitKraken-v6.5.1.tar.gz)

- mac:

[release.axocdn.com/darwin/GitKraken-v6.5.1.zip](release.axocdn.com/darwin/GitKraken-v6.5.1.zip)

[release.gitkraken.com/darwin/GitKraken-v6.5.1.zip](release.gitkraken.com/darwin/GitKraken-v6.5.1.zip)

### 配置 host 文件避免软件自动更新

```
  127.0.0.1 release.gitkraken.com api.gitkraken.com
```
