---
date: '2025-04-21T12:43:34+08:00'
lastmod: '2025-04-21T12:43:34+08:00'
draft: false
title: 'Hugo 学习笔记'
description: "随缘装修"
math: true
toc: true
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
series_order: 2       # 文章在系列中的顺序
categories:
 - '📝便签'
tags:
  - Hugo 
---

- 文件名不能重

> 解决:按日期组织文件

## Hugo >> series

> 动机: hugo 文章的顺序只是按时间顺序组织, 就有点不太好

## 添加描述

- 修改: layouts $>>$ partials $>>$ tile.html

```html
        <div class="article-details">
            <h2 class="article-title">
                {{- .context.Title -}}
            </h2>

            <!-- 添加这部分代码来显示文章描述 -->
            {{ if .context.Description }}
            <div class="article-description">
                {{ .context.Description }}
            </div>
            {{ else if .context.Summary }}
            <div class="article-description">
                {{ .context.Summary | truncate 150 }}
            </div>
            {{ end }}     
             <!-- 描述显示代码结束 -->
            
            {{ if eq .Type "taxonomy" }}
                <span class="article-count">{{ len .context.Pages }} 篇文章</span>
            {{ end }}
        </div>
```

