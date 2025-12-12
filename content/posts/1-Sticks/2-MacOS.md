---
date: 2025-05-05T18:45:50+08:00
lastmod: 2025-05-05T18:45:50+08:00
draft: false
title: MacOS 自定快捷指令
math: true
toc: true
categories:
  - '📝便签'
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
tags:
  - MacOs
---

[关于如何在 mac 上进行插入日期和时间的做法](https://blog.csdn.net/Archiena/article/details/130336634)

(1) 设置快捷操作

- 聚焦搜索：输入自动操作

- 打开自动搜索，选择 快速操作，选择实用工具，双击选择 shell 脚本

- 输入 date +"%Y-%m-%d %H:%M:%S %A"

- shell 脚本中，需要额外注意的设置是，输入部分选择 无输入 ，输出选择替换文本 ，点击运行，程序运行，点击结果，查看结果

- 保存快速操作名称 日期快捷键

打开系统设置，键盘，快捷键，服务，文本，选择找到 日期快捷键，没有可以稍等一下，录入快捷键即可。

```bash
date +"%Y-%m-%dT%H:%M:%S+08:00"
```

(2)  设置系统快捷键

系统设置(option+0) >> 键盘 >> 键盘快捷键 >> 服务 >> 本文 >> 你存储的文件名 >>  录入快捷键

- 时间快捷键 $\uparrow$ +`^` + `T`   19:11:00 
- 日期快捷键 $\uparrow$ +`^` + `D`   2025-05-05 Monday 
- hugo 新建元数据快捷键 $\uparrow$ +`^` + `H`   2025-05-05T19:11:06+08:00



