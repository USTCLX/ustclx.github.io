---
title: vscode extension 开发debug时卡在building的原因和解决
date: 2021-11-06T20:28:36+08:00
tags:
  - vscode extension
---

## 背景

最近在开发一款 vscode 插件，遇到了一个问题。特此记录。

## 问题描述

1. 使用 vscode extension 官方的 cli 生成项目模版
2. 开始 debugger 开发，一切正常，按 F5 自动弹出一个新的 vscode
3. 升级项目中使用的 webpack 版本
4. 使用 debugger 开发，按 F5 不会在自动弹出新的 vscode，卡在了 build 阶段，如下图

![vscode_extension_debug_problem](/images/vscode_extension.png)

## 问题原因和解决

在 google 了很久之后，总算确定了问题的原因 😳。

vscode extension 在 debug 的 watch 模式下，会监听控制台 webpack 的输出。上图的截图，控制台中显示的家就是 webpack 的输出。
然后在项目的 task.json 中的 endsPattern 字段，会去匹配这个输出的字符串，如果匹配上了，就认为构建 ok 了，然后弹出一个新的 vscode。

问题的根据就在于升级 webpack 之后，webpack 的输出变了，因此需要重写一下。如下图。

![vscode_extension_debug_fix](/images/vscode_extension_fix.png)
