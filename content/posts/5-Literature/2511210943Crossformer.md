---
slug: 7979582fa1
draft: false
lightgallery: false
password:
message:
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg # 可能报错
license:
comment: true
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
title: 'ICLR-2023.03-Crossformer'
date: '2025-11-21T09:43:09+08:00'

# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github BugFix
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点'
categories:
  - '📜文献阅读'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

## 文献基本信息

- 文章标题：Crossformer: Transformer Utilizing Cross-Dimension Dependency for Multivariate Time Series Forecasting
- 作者团队：上海交通大学
- 源码链接：[https://github.com/Thinklab-SJTU/Crossformer](https://github.com/Thinklab-SJTU/Crossformer) 
- 原文链接：[https://openreview.net/pdf?id=vSVLM2j9eie](https://openreview.net/pdf?id=vSVLM2j9eie)

## 关键词

多变量间相关性建模；patch；通道独立；变量维度建模

## 相关论文

- Crossformer: Transformer Utilizing Cross-Dimension Dependency for Multivariate Time Series 
- iTransformer: Inverted Transformers Are Effective for Time Series Forecasting
- TimeMixer: Decomposable Multiscale Mixing for Time Series Forecasting
- PatchTST ：A Time Series is Worth 64 Words: Long-term Forecasting with Transformers

## pre1：多变量时序预测

- 解决的科学问题：多变量时序预测，输入是多个变量的历史序列，需要同时预测这些变量在未来的取值
- 多变量时序预测记作 MTS
- MTS 预测有利于下游任务的决策，广泛应用于天气、能源、金融等多个领域

$$
X_{1:T} \in \mathbb{R}^{T \times D}  \rightarrow X_{T+1:T+\tau} \in \mathbb{R}^{\tau \times D}
$$

![image.png](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511210941583.png)

## pre2：通道独立&通道混合

- 建模多变量时序（MTS）预测的两种一般方法：通道独立 与 通道依赖（也可叫做通道混合）
- 通道 指的就是变量
- 通道独立（channel independent）：把多元时序预测问题转化为单变量时序预测问题。每个变量独立建模，就视为一个单变量时序预测任务。完全不考虑各个变量之间的关系
- 通道依赖（channel dependent）：将多变量之间的关系考虑在内，模型中同时建模时序信息和变量间的关系信息

![image.png](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511210941584.png) 

## 研究现状

通道依赖关系非常依赖模型的构建，否则很容易出现过拟合 

## 研究动机

- 作者认为之前 Transformer-based 的模型，更专注于融合建模，直接把每个时刻的各个变量融合成一个 Embedding，输入后续模型
- 这种方法存在的问题是，只专注建模跨时间依赖，对跨变量依赖关注不足
- 具体来说，仅通过 Embedding 隐式地利用这种依赖关系

![image.png](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511210941585.png) 

- point-wise token：单个时间步不像句子中一个词那样具有语义含义，因此提取局部语义对于时序分析至关重要；可以考虑从时间步 token 转化为时间段 token
- 借鉴 ViT 的思想：将图像分割成不重叠的中型小块 patch，然后将这些小块重新排列成一个序列输入到 Transformer 中
- 可视化 MTS 的注意力图：临近的数据点具有相似的注意力权重
- SwinTransformer：分 patch，局部注意力，分层设计，逐层合并并提取多尺度特征
- **三种主流的 token（时间步 token、分段 token、一条时间序列揉成一个 token 即变量级别的 token）**

![image.png](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511210941586.png) 

## 创新点

显式建模跨变量依赖（或者 变量间关系）

- DIMENSION-SEGMENT-WISE EMBEDDING||  维度-分段-Wise（DSW） 嵌入 -》参考 ViT
-  TWO-STAGE ATTENTION LAYER  ||  两阶段注意力层（TSA 层）-》参考 SwinTransformer
-   HIERARCHICAL ENCODER-DECODER ||  分层编码器-解码器（HED）-》参考 SwinTransformer

### 段级别的 Embedding

-  DSW Embedding ： 段级别的 Token Embedding
- 关键点：先分段+再做 Embedding，这样可以保留局部时间模式，同时显式分离时间和维度信息，为后续注意力提供结构化输入

![image.png](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511210941587.png) 



- D：变量个数
- i 表示时间
- $h_{i,d}$ 代表单变量时间序列片段，将一维向量分割成片段，以计算分段相关性，从而增强局部相关性并降低计算复杂度

### 两阶段注意力层

对于得到的二维数组 $H$，可以将其展平为一位序列，类似 ViT 的处理，但是对于多变量时序预测来说：
1. 图像中的高度和宽度可以互相交换，但是对于 MTS 来说，时间轴和维度轴有不同的含义，因此应区别对待
2. 直接对二维数组应用自注意力会导致复杂度为 $O(D^2\frac{T^2}{L_{seg}^2})$，对于较大的 D 来说，计算机太大了

基于此，作者提出两阶段注意力层来捕捉二维向量数组中的跨时间和跨维度的依赖关系

![image.png](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511210941588.png) 

图2：TSA层

(a) 用于处理表示多变量时间序列的二维向量数组的两阶段注意力层：每个向量都对应原始序列的一个片段。整个向量数组经过跨时间阶段和跨维度阶段，以获取相应的依赖关系。

(b) 在跨维度阶段直接使用MSA来建立$D$ 对 $D$ 的连接，会产生$O(D^{2})$ 的复杂度。

(c) 跨维度阶段的路由机制：少量固定数量 $c$ 的“路由器”从所有维度收集信息，然后分发所收集的信息。复杂度降低到 $O(2cD)=O(D)$ 。

- TSA 层的输入 $Z \in \mathbb{R}^{L×D×d_{model}}$ 

- 两阶段注意力层，分别进行跨时间段建模 $Z_{:,d}$ 和 跨维度建模 $Z_{i,:}$

TSA 层以后，任意变量的任意时间段都被联系起来。

- 总结起来就是两阶段 attention，第一阶段在时间维度进行 attention，第二阶段在多变量之间进行 attention
- i.e. 输入先过一层时间维度 attention，独立的进行每个序列时序上的建模；然后再输入到一层空间维度 attention，对齐不同变量各个时间步的编码

### 分层编码器 & 解码器


