---
slug: 6a465cff44
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
  avatar:  /images/avatar.jpeg 
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
title: 备忘录 | Shell语言
subtitle:
date: 2025-11-20T20:06:38+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github
tags:
  - Shell
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点'
categories:
  - '⛄好用的代码片'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->

### 控制脚本看到几张卡

```shell
export CUDA_VISIBLE_DEVICES=1
```

### 脚本配置文件

> 打开 launch.json、点击小虫子

```bash
{
    // 使用 IntelliSense 了解相关属性。 
// 悬停以查看现有属性的描述。
// 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
"version": "0.2.0",
"configurations": [

    {
        "name": "[这里更换为任意名称]",
        "type": "python",
        "request": "attach",
        "connect": {
            "host": "localhost",
            "port": 5998
        }
    },
    {
        "type": "bashdb",
        "request": "launch",
        "name": "Bash-Debug (type in script name)",
        "cwd": "${workspaceFolder}",
        "program": "${command:AskForScriptName}",
        "args": []
    },
    {
        "type": "bashdb",
        "request": "launch",
        "name": "Bash-Debug (select script from list of sh files)",
        "cwd": "${workspaceFolder}",
        "program": "${command:SelectScriptName}",
        "args": []
    },
    {
        "type": "bashdb",
        "request": "launch",
        "name": "Bash-Debug (hardcoded script name)",
        "cwd": "${workspaceFolder}",
        "program": "${workspaceFolder}/path/to/script.sh",
        "args": []
    },
    {
        "type": "bashdb",
        "request": "launch",
        "name": "Bash-Debug (simplest configuration)",
        "program": "${file}"
    }
]
}
```

### 脚本调试设置

> ① 需要vscode 插件市场，安装 `Bash Debug`插件，才能给 sh 文件打断点
>
> ② 监听端口记得保持一致

```shell
pip install -U debugpy

python -u run.py

python -m debugpy --listen 5998 --wait-for-client run_longExp.py
```

