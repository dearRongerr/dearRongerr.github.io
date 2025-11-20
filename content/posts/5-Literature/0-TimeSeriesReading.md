---
date: '2025-05-01T20:54:16+08:00'
lastmod: '2025-05-01T20:54:16+08:00'
draft: false
title: '📌 时序论文阅读'
math: true
# 'Hugo'  ,  'Python'  ,  'DeepLearning'  ,  'Statistics','VSCode'
# categories: ['Pinned']
# series: [""]  # 添加此行，用于系列文章
# series_order: 1  # 添加此行，用于指定文章在系列中的顺序
# '注意力机制'  ,  '入门'  ,  'Sticks':注意事项,  'TODO'
# tags: ['汇总向']
pinned: true
toc: false
categories:
  - '📜文献阅读'
---

[https://dearrongerr.github.io/Rongerr.github.io/literature/TSP/](https://dearrongerr.github.io/Rongerr.github.io/literature/TSP/)

|         期刊/年份          | 模型         |                           论文题目                           | 代码                                                         | 作者团队                      | 复现笔记                                                     | 创新点                                                       |
| :------------------------: | ------------ | :----------------------------------------------------------: | ------------------------------------------------------------ | ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
|        NeurIPS2021         | Autoformer   | [Autoformer: Decomposition Transformers with Auto-Correlation for Long-Term Series Forecasting](https://arxiv.org/pdf/2106.13008) | [https://github.com/thuml/Autoformer](https://github.com/thuml/Autoformer) | 清华大学吴海旭                | [点击跳转](https://github.com/thuml/Autoformer)              | 序列分解,自相关注意力                                        |
|        NeurIPS2022         | TDformer     | [First De-Trend then Attend:Rethinking Attention for Time-Series Forecasting](https://arxiv.org/pdf/2212.08151) | -                                                            | 美国在读PHD, 在亚马逊AI实验室 |                                                              | ①从数学的角度证明线性注意力条件下,时域频域小波域注意力的的等价<br />②STL 分解序列,趋势项 MLP,季节项傅里叶注意力 |
|                            | SegRNN       | [SegRNN: Segment Recurrent Neural Network for Long-Term Time Series Forecasting](https://arxiv.org/pdf/2308.11200) | [https://github.com/lss-1138/SegRNN](https://github.com/lss-1138/SegRNN) | 华南理工大学                  | [点击跳转](https://dearrongerr.github.io/Rongerr.github.io/Reproduction/5_SegRNN_v1/) | 将时间序列分段,然后嵌入,先交换维度,分段,然后送入 RNN 迭代    |
|          ICLR2023          | TimesNet     |    [作者知乎专栏](https://zhuanlan.zhihu.com/p/606575441)    | [自己摘出模型主要部分的 git 仓库](https://github.com/dearRongerr/TSF_Reproduction/tree/main/250502-TimesNet) | 清华软院机器学习组,吴海旭     | [点击跳转](https://dearrongerr.github.io/p/timesnet%E4%BB%A3%E7%A0%81%E5%A4%8D%E7%8E%B0-%E7%AC%AC%E4%B8%80%E7%AF%87/) | 时序数据,一维转二维,<br />encoderOnly<br />topk 识别傅里叶能量最高的周期 96 -> 4天×24小时 数据<br />参数比较多,init 就有点慢 |
| ICLR 2024<br />(Spotlight) | iTransformer | [ITRANSFORMER: INVERTED TRANSFORMERS AREEFFECTIVE FOR TIME SERIES FORECASTING](https://arxiv.org/pdf/2310.06625) | [https://github.com/thuml/iTransformer](https://github.com/thuml/iTransformer) | 清华软院机器学习组，阿里集团  |                                                              |                                                              |



## 时序论文相关工作

分成频域模型和时域模型叙述的

实际上应该是: 

RNN 系,former 系,Linear 系

单输入,多输入

短时预测,长时预测
