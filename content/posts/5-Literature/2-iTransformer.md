---
date: '2025-05-02T21:43:38+08:00'
lastmod: '2025-05-02T21:43:38+08:00'
draft: false
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
title: '文献阅读iTransformer'
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

- 论文题目：ITRANSFORMER: INVERTED TRANSFORMERS AREEFFECTIVE FOR TIME SERIES FORECASTING
- 作者团队：清华软院机器学习组，阿里集团
- 文章收录：ICLR 2024（Spotlight）
- github地址：[https://github.com/thuml/iTransformer](https://github.com/thuml/iTransformer)

## ABSTRACT

The recent boom of linear forecasting models questions the ongoing passion for architectural modifications of Transformer-based forecasters. These forecasters leverage Transformers to model the global dependencies over temporal tokens of time series, with each token formed by multiple variates of the same timestamp. However, Transformers are challenged in forecasting series with larger lookback windows due to performance degradation and computation explosion. Besides, the embedding for each temporal token fuses multiple variates that represent potential delayed events and distinct physical measurements, which may fail in learning variate-centric representations and result in meaningless attention maps. In this work, we reflect on the competent duties of Transformer components and repurpose the Transformer architecture without any modification to the basic components. We propose iTransformer that simply applies the attention and feed-forward network on the inverted dimensions. Specifically, the time points of individual series are embedded into variate tokens which are utilized by the attention mechanism to capture multivariate correlations; meanwhile, the feed-forward network is applied for each variate token to learn nonlinear representations. The iTransformer model achieves state-of-the-art on challenging real-world datasets, which further empowers the Transformer family with promoted performance, generalization ability across different variates, and better utilization of arbitrary lookback windows, making it a nice alternative as the fundamental backbone of time series forecasting.

