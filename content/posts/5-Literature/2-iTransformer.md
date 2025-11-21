---
date: '2025-05-02T21:43:38+08:00'
lastmod: '2025-05-02T21:43:38+08:00'
draft: false
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
title: ICLR-2024.03-iTransformer
math: true
# 'Hugo'  ,  'Python'  ,  'DeepLearning'  ,  'Statistics','VSCode'
# categories: ['PaperReading']
# series: [""]  # 添加此行，用于系列文章
# series_order: 1  # 添加此行，用于指定文章在系列中的顺序
# '注意力机制'  ,  '入门'  ,  'Sticks':注意事项,  'TODO'
# tags: ['时间序列']
toc: true
categories:
  - '📜文献阅读'
---

ICLR-2024.03-iTransformer

## 文献基本信息

- ITRANSFORMER: INVERTED TRANSFORMERS AREEFFECTIVE FOR TIME SERIES FORECASTING
- 作者团队：清华软院机器学习组，阿里集团
- 源码地址：[https://github.com/thuml/iTransformer](https://github.com/thuml/iTransformer) 
- intro(CN)：[https://mp.weixin.qq.com/s/-pvBnA1_NSloNxa6TYXTSg](https://mp.weixin.qq.com/s/-pvBnA1_NSloNxa6TYXTSg)

## 关键词

以变量为主体的特征表示

## 研究动机

- 线性模型质疑了 Transformer 在 MTS（多变量时序预测）的有效性

- 本文研究了为什么 former 系的效果甚至不如 Linear 系，本文认为 former 系效果不好是因为 Token 的不恰当使用

  - 对于时间序列预测输入数据 $(B,T,D)$，以前的嵌入是对 D 嵌入到 $d_{model}$
  - iTransformer 的视角，把 T 嵌入到 $d_{model}$

![image.png](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511210904244.png)

## 创新性 

- 将独立的时间序列视为 Token 
- 因为 Transformer 的输入转置了 ，所以，注意力模块和前馈网 FFN 职责也变了
  - 现在的注意力模块 学习的是多变量的相互关系
  - 现在的 FFN 提取的是序列内时间的相关性

## 模型结构

![image.png](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511210904246.png)

1. 嵌入层：同一个观测变量的时间序列进行映射
2. 多头自注意力层：捕获变量与变量之间的相关性，此时的注意力得分矩阵的形状是 $变量数 × 变量数 $，而不是以前的计算时间步与时间步之间的相关性
3. FFN 前馈神经网络层，对历史信息进行编码 $d_{model} \rightarrow d_{ff}$；然后对未来序列进行解码 $d_{ff} \rightarrow d_{model}$
4. 归一化层：作用于Variate Token内部，让所有变量的特征都处于相对统一的分布下，减弱测量单位的差异。这种方式还可以有效处理时间序列的非平稳问题问题




