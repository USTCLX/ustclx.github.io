---
title: 使用PicGo和Github打造自己的图床
date: 2022-04-24 15:49:21
tags:
  - 实用工具
---

平时在写 blog 的时候，难免会需要使用一些图片，目前是把图片作为静态资源的一部分放在仓库里，但是这种方式的弊端也很明显，如果我想把 blog 发布到不同的平台上，就需要在每个平台分别上传，使用图床来统一管理图片，在不同的平台都可以方便的引用，这样就方便多了。

网上调研了一下，发现用 github 作为图片的存储空间，配合一个叫做 PicGo 的软件，可以很方便的实现我的需求。下面就是具体的操作步骤。

1. 在 github 上创建一个仓库来专门存放我们的图片。
   ![create_repo](https://raw.githubusercontent.com/USTCLX/images/main/static/20220424160232.png)
2. 访问：https://github.com/settings/tokens，生成一个token用于PicGo操作你的仓库
   1. 然后点击 Generate new token。
      ![token](https://raw.githubusercontent.com/USTCLX/images/main/static/20220424160411.png)
   2. 把 repo 的勾打上即可。然后翻到页面最底部，点击 Generate token 的绿色按钮生成 token。
      ![](https://raw.githubusercontent.com/USTCLX/images/main/static/20220424160541.png)
3. 配置 PicGo。
   1. **注意：**仓库名的格式是用户名/仓库，比如我创建了一个叫做 test 的仓库，在 PicGo 里我要设定的仓库名就是 Molunerfinn/test。
   2. 一般我们选择 main 分支即可。然后记得点击确定以生效，然后可以点击设为默认图床来确保上传的图床是 GitHub。
      ![](https://raw.githubusercontent.com/USTCLX/images/main/static/20220424160725.png)
