---
layout: post
title: 记录我的GitHub Pages博客建立过程
author:
  name: Zhen Zhang
  link: https://github.com/zhnzhang
categories:
- Blogging
- Blog
tags:
- blog
date: 2022-02-18 16:44 +0800
---
用GitHub Pages建立一个博客很简单，只要建立一个<username>.github.io的特殊仓库就可以了（<username>代表所用github账号的用户名）。这篇博客里主要记录了利用Jekyll Theme装点我的博客时踩过的一些坑。

我选用的主题是[Chirpy Jekyll Theme](https://github.com/cotes2020/jekyll-theme-chirpy)，根据[配置文档](https://chirpy.cotes.page/posts/getting-started/)进行操作：

- 安装[jekyll](https://jekyllrb.com/docs/installation/)时

  - mac用户建议用brew安装rbenv管理多个ruby版本
  - 用gem安装Jekyll时可能会提示PATH配置有问题，gem不能运行，安装Jekyll后会出现找不到命令的情况，这时候只要将它提示的***PATH***，用export PATH=${PATH}:***PATH***加入zshrc或者bashrc文件即可

- 我选用[Using the Chirpy Starter](https://chirpy.cotes.page/posts/getting-started/#option-1-using-the-chirpy-starter)的方式建立<username>.github.io仓库，接下来需要：

  - 将内容clone到本地，并运行bundle命令

  - 然后直接看[Deployment](https://chirpy.cotes.page/posts/getting-started/#deployment)这一小节的内容

    - 修改 **_config.yml**文件，尤其是url要配置正确，否则部署后网页会blank，详见[issue](https://github.com/cotes2020/jekyll-theme-chirpy/issues/502)。

    - 如果runtime system不是linux，记得使用下面的命令update the platform list in the lock file

    - ```ruby
      bundle lock --add-platform x86_64-linux
      ```

  - 接着按照[Deploy by Using Github Actions](https://chirpy.cotes.page/posts/getting-started/#deploy-by-using-github-actions)这一小节内容照做即可
