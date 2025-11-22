---
date : '2025-11-19T09:45:28+08:00'
draft : false
title : 'vscode + hugo + Typora 工作流'
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱'
categories :
    - '⛄好用的代码片'
# 'Python' 'Hugo' ''Shell' 'SOP' 'Typora' 'VsCode'
tags : 
    - 'SOP'
    - 'Hugo'
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg 
---



**toml**
- `+++`包起来的是 toml 文件
- 只能用 `=`

**yaml**
- `---`包起来的是yaml 文件
- 只能用 `:`

Hugo + FixIt文件组织形式

- category 给结构，tag 给检索，collections 给故事线

**⌘⇧B（Mac）**

（1）本地写完，准备推 GitHub Pages 前

（2）打包上传服务器，拿 public/ 文件夹

**vscode + hugo + Typora 工作流**

1. Hugo Helper 插件：F1 → Hugo: New Content → 回车
2. Open in Typora插件：右键 Open in Typora
3. CML：hugo serve