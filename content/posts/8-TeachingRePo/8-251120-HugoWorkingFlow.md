---
slug: ab5c663878
draft: false
lightgallery: false
password:
message:
repost:
  enable: false
  url:
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg # 可能报错
license:
comment: true
weight: 0
hiddenFromHomePage: false
hiddenFromSearch: false
hiddenFromRelated: false
hiddenFromFeed: false
resources:
  - name: featured-image
    src: featured-image.jpg
  - name: featured-image-preview
    src: featured-image-preview.jpg
toc: true
math: true
title: 我的 Hugo 工作流
subtitle:
date: 2025-11-20T20:17:17+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github
tags:
  - Hugo
  - VsCode
  - BugFix
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点'
categories:
  - '🍎来跟我点点点'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->

## 工作流

- `hugo serve` 启动，本地预览效果

- `🌐+F1` 在 `Post/` 下新建 `.md` 文件

- 修改：标题、tag、分类

- md 文件，右键，`open in Typora`   （VsCode 插件：Open in Typora）

- 写完以后：图片上传图床 `格式>>图像>>上传所有本地图像`

- vscode 仓库管理，输入`commit message`，提交

- 去远程仓库 [https://github.com/dearRongerr/dearRongerr.github.io](https://github.com/dearRongerr/dearRongerr.github.io) acitons 查看部署

  

## **我的自动化部署文件**



> 尤其值得注意
>
> - `Prevent Jekyll` 禁用所有的 Jekyll，不然部署会报错
>
> - 部署的逻辑是：
>
>   - 本地仓库是 git init
>   - 远程仓库在 github 上操作，新建一个新的干净的仓库：new Repositories>>仓库命名 dearRongerr.github.io （这样可以保证生成链接是 https://用户名.io）>>  settings >> Pages >> source >> Github Actions
>   - 现在连接本地仓库和远程仓库：关键是 `deploy.yml` 这个自动化部署文件，可以每次提交源码，然后yml 文件新建 public 文件，部署到自己的链接 .github>>workflows>>XXXX.yml
>   - 然后推送自己本地的仓库到远程就会自动执行部署。
>
> - 目前看到的部署方式：
>
>   - ①个人 token 部署 
>
>     ②GITHUB_TOKEN部署 (我提供的 yml 文件采用这种方式) 
>
>     ③源代码一个仓库 `hugo_blog` ，存静态网页的一个仓库 `用户名.github.io` 



```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ['main']

  workflow_run:
    workflows: ['Update theme']
    types:
      - completed

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: 'pages'
  cancel-in-progress: true

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v1
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: ${{ vars.HUGO_VERSION || 'latest'}}
          extended: true
      - name: Clean last public
        run: rm -rf public
      - name: Build with Hugo
        run: hugo --logLevel=warn --gc --minify --baseURL ${{ steps.pages.outputs.base_url }}
      - name: Prevent Jekyll
        run: touch public/.nojekyll
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4

```

## 本站点从 0 到 1 的搭建

- 看到喜欢的 hugo fixIt样式：

```bash
git clone https://github.com/xueweiwujxw/xww_blog.git
```

- hugo serve 看到完全一模一样的页面

> 会报错：`Page Not Found`
>
> - `ls -l themes/`   检查主题文件夹，出现
>
>   > total 0
>   > drwxr-xr-x@ 2 dearr  staff  64 11 19 12:25 FixIt
>   > drwxr-xr-x@ 2 dearr  staff  64 11 19 12:25 cmpt-flyfish
>   > drwxr-xr-x@ 2 dearr  staff  64 11 19 12:25 cmpt-translate
>   > drwxr-xr-x@ 2 dearr  staff  64 11 19 12:25 shortcode-docs-bookmark
>
>   错因：目录全是 **64 B 的空壳**（git 只建了子模块目录，没下代码）
>
>   修复：在站点根目录执行：
>
>   ```bash
>   # 拉取所有子模块（主题 + 组件）
>   git submodule update --init --recursive
>   ```
>

- 逐步修改就好
- 编写yml部署文件
- 查看远程仓库连接
- 断开远程仓库连接
- 新建远程仓库
- 推送本地仓库到远程仓库
