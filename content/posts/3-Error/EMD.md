---
date: '2025-04-27T20:03:45+08:00'
lastmod: '2025-04-27T20:03:45+08:00'
draft: false
title: 'EMD库安装'
math: true
# 'Hugo'  ,  'Python'  ,  'DeepLearning'  ,  'Statistics','VSCode'
# categories: ["Python"]
# # series: [""]  # 添加此行，用于系列文章
# # series_order: 1  # 添加此行，用于指定文章在系列中的顺序
# # '注意力机制'  ,  '入门'  ,  'Sticks':注意事项,  'TODO'
# tags: ['库安装','捉个虫','TimeSeries']
toc: true
tags:
  - Python
categories:
  - '🕷️捉个虫'
---

卸载

```Python
pip uninstall EMD-signal
```

注意:

① 与 python 3.6 版本不兼容

② 这里的库名称: `EMD-signal`

③ 建议 python 版本 3.8

```bash
conda create -n py38 python=3.8
conda activate py38
pip install EMD-signal
pip install statsmodels pandas matplotlib scipy
```

自动确认所有安装提示,`-y`

```bash
conda install scikit-learn -y
```
