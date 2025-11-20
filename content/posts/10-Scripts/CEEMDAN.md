---
date: '2025-04-30T15:10:35+08:00'
lastmod: '2025-04-30T15:10:35+08:00'
draft: false
title: 'CEEMDAN'
math: true
# 'Hugo'  ,  'Python'  ,  'DeepLearning'  ,  'Statistics','VSCode'
# categories: ['TimeSeries']
# series: [""]  # 添加此行，用于系列文章
# series_order: 1  # 添加此行，用于指定文章在系列中的顺序
# '注意力机制'  ,  '入门'  ,  'Sticks':注意事项,  'TODO'
# tags: ['例子','应用']
toc: true
categories:
  - '🖇️碎笔论文'
---

## 全称

CEEMDAN

自适应噪声完备集合经验模态分解

Complete Ensemble Empirical Mode Decomposition with Adaptive Noise

## 注意

① 要注意这个方法并不是在CEEMD方法上改进而来的，而是从EMD的基础上加以改进，② 同时借用了EEMD方法中加入高斯噪声和通过多次叠加并平均以抵消噪声的思想。

## 分解流程

### EMD 分解流程

![image-20250430152605940](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301546432.png) 

### CEEMDAN 分解流程

![毕业论文插图-第 6 页](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301546433.png)

## EEMD与CEEMDAN的区别

- EEMD方法是将添加白噪声后的M个信号直接做EMD分解，然后相对应的IMF间直接求均值；

- CEEMDAN方法是每求完一阶IMF分量，又重新给残值加入白噪声（或白噪声的IMF分量）并求此时的IMF分量均值，并逐次迭代。

## 注意

(1) 如果最终$ r_k $也算一个$IMF$分量的话，上图总共分解出了$K+1$个$IMF$分量；

(2) 图中$ E_k(∗) $为利用$EMD$算法产生的第$k$阶$IMF$分量，所以每次迭代添加都是最初始的那些白噪声的$IMF$分量（第一次迭代添加的是原始白噪声）。

## 优势

CEEMDAN 相对于 EEMD 的优势

1. 完备性,即分解后的各个分量相加能够获得原信号的性质,CEEMDAN 在较小的平均次数下就可以有很好的完备性,而对于 EEMD 方法,较小的平均次数会导致交叉的完备性,也就是重构误差会很大
2. 更快地计算速度,相比于 EEMD,CEEMDAN 不需要太多的平均次数
3. 更好的模态分解结果,EEMD 分解可能会出现多个幅值很小的低频分量,这些分量对信号分析意义不大,CEEMDAN 方法可以减少这些分量数量
