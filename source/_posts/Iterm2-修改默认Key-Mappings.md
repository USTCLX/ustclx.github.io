---
title: Iterm2 修改默认Key Mappings
date: 2022-04-24 16:55:12
tags:
  - Iterm2
  - 奇奇怪怪的问题的解决方案
---

平时使用 Mac 工作，安装了 Iterm2 来取代默认的 Terminal 软件。但是 Iterm2 的按键却非常不符合平时的习惯。

比如删除一整行这个操作

- 在类似 VSCode 这样的编辑器中，都是使用`Cmd + delete`这样的按键组合
- 但是在 Iterm2 中，却是`Control + U` 这样的按键组合

可以通过如下方式修改 Iterm2 的 Key Mappings

### 1. 打开 Preferences

![](https://raw.githubusercontent.com/USTCLX/images/main/static/20220424170010.png)

### 2. 进入 Profiles -> Keys -> Key Mappings。选择 Natural Text Editing

![](https://raw.githubusercontent.com/USTCLX/images/main/static/20220424170216.png)

大工告成，可以愉快的使用`Cmd + delete`这样的按键组合了。
