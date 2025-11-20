---
date: '2025-05-02T20:21:54+08:00'
lastmod: '2025-05-02T20:21:54+08:00'
draft: false
title: 'RuntimeError No CUDA GPUs are available'
math: true
# 'Hugo'  ,  'Python'  ,  'DeepLearning'  ,  'Statistics','VSCode'
# categories: ['Python']
# series: [""]  # 添加此行，用于系列文章
# series_order: 1  # 添加此行，用于指定文章在系列中的顺序
# '注意力机制'  ,  '入门'  ,  'Sticks':注意事项,  'TODO'
# tags: ['捉个虫','论文复现']
toc: true
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
categories:
  - '🕷️捉个虫'
tags:
  - Python
---

RuntimeError: No CUDA GPUs are available

![image-20250502202207929](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202505022025082.png)

卸载当前环境:

```python
pip uninstall -y torch torchvision torchaudio
```

尝试 1

```Python
conda install pytorch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 pytorch-cuda=11.8 -c pytorch -c nvidia -y
```

报错 Again

解决: `True → False`

```Python
parser.add_argument('--use_gpu', type=bool, default=False, help='use gpu')
```

毕竟只是想调试,查看功能,是环境的冲突问题,换个纯净的,新的就没事了,这里就先这样吧
