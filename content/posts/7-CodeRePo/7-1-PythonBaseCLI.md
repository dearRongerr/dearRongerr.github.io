---
slug: 64c9be7
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
title: 备忘录 | Python 基础命令
subtitle:
date: 2025-11-20T19:23:17+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib
tags:
  - Python
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱'
categories:
  - '⛄好用的代码片'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

存一些最最基础的命令，我一点也记不住，就说，记下了就是我的了 😂

## pip 管理环境

pip 的虚拟环境管理包 virtualenv

```bash
python3 -m pip install virtualenv --user
python3 -m virtualenv SegRNN
source SegRNN/bin/activate
pip install -r requirements.txt
```

## conda 管理环境

```bash
conda create -n dave python==3.8
conda activate dave

pip install -r requirements.txt

conda install pytorch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 pytorch-cuda=11.8 -c pytorch -c nvidia
conda install numpy
conda install scikit-image
conda install scikit-learn
conda install tqdm
conda install pycocotools

# 激活 & 退出
conda activate Autoformer
conda deactivate
```

### 查看虚拟环境列表

```Python
conda env list
conda actiavte 环境名
conda deactivate
```


