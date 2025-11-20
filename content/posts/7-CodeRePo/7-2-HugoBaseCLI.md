---
date : '2025-04-21T08:26:34+08:00'
draft : false
title : '备忘录 | Hugo基础命令'
math : true
categories :
- '⛄好用的代码片'
tags : 
 - 'Hugo'
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
---

## 新建站点

```go
hugo new site hugoblog
```

## 清楚缓存

```go
hugo -F --cleanDestinationDir
```

## 新建文章

```go
hugo new content/post/Hugo/index.md
hugo new content/post/04/21/DEA-Net.md
```

## 启动 serve

```go
hugo server -D --navigateToChanged
hugo server
hugo server -D
hugo server --port 1313
hugo --cleanDestinationDir && hugo serve
```

## 推送远程

首先,根目录执行:

```bash
hugo
```

进入: `public` 文件夹

```bash
cd public
```

接着命令行执行&推送:

```bash
git add .
git commit -m"Date"
git push origin main https://github.com/dearRongerr/dearRongerr.github.io.git
```

## 元数据设置

```go
---
date: 2025-05-05T18:45:50 +08:00
draft: false
title: MacOS 自定快捷指令
math: true
toc: true
categories: ['Hugo']
tags: ['Python']
series: ["Hugo + Obsidian"]
series_order: 1       # 文章在系列中的顺序
---
```
