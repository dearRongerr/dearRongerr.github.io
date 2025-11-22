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
summary: 存一些最最基础的命令，我一点也记不住，就说，记下了就是我的了 😂
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---



前言：关于python 环境管理

- pip 创建虚拟环境
- pip 激活虚拟环境
- conda 创建虚拟环境
- conda 激活虚拟环境
- conda 查看虚拟环境列表 `conda env list ` 
- pip 没有专门的虚拟环境管理列表
- VsCode 的 GUI & CLI 环境的对应 ① GUI 环境右下角 ② CLI 环境 command + shift + P 配置



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

## 打印模型结构

（1）打印init的模型结构，并不是真正使用的结构，表示都初始化了什么层

```python
model = Model(args).float()
# print(model)
# 最简单最小改动 将这句的输出结果写入.log文件
with open("model.log", "a", encoding="utf-8") as f:
    print(model, file=f)
```

(2) summary，进行一次完整的反向传播数据经过了哪些层

单变量输入

```python
# 单变量输入
pip install torchinfo
from torchinfo import summary
model = Model(configs)
x = torch.randn(batch_size, seq_len, enc_in)
summary(model, input_data=x)
```

多变量输入

```python
#多变量输入
summary(model, input_data=[batch_x,x_mark_enc])
```




输出写入到文件，直接替换掉以前的 summary那句 即可

```python
# 最简单最小的改动 summary 输入到summary_model.log 文件中
from torchinfo import summary
import sys
import io
# 捕获标准输出
old_stdout = sys.stdout
sys.stdout = new_stdout = io.StringIO()

# 执行 summary 函数
summary(model, input_data=[batch_x, x_mark_enc])

# 恢复标准输出
sys.stdout = old_stdout

# 将捕获的输出写入文件
with open("summary_model.log", "w") as log_file:
    log_file.write(new_stdout.getvalue())
```

