---
date: '2025-04-30T11:08:19+08:00'
lastmod: '2025-04-30T11:08:19+08:00'
draft: false
title: 'CEEMD'
math: true
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg 
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

- ## 全称:

> Complementary Ensemble Empirical Mode Decomposition
>
> 互补集合经验模态分解

- ## WHY? 对比先前的进步:

> CEEMD优化的是EEMD的“残余辅助噪声”
>
> **为什么会有残余辅助噪声呢？** 
>
> 因为EEMD的前提是认为“多组白噪声的叠加近似等于0”。然而当处理的次数不够多的时候，白噪声往往不能被降低到忽略不计的程度。
>
> 反过来讲，如果使用EEMD方法时想要获得残余噪声较小的结果，就需要增加平均处理的次数，这样无疑会增加计算量。

- ## HOW? CEEMD 解决的思路

> 将一对互为相反数的正负白噪声作为辅助噪声加入源信号当中，以消除原来 EEMD 方法分解后重构信号当中残留的多余的辅助白噪声，同时减少分解时所需的迭代次数，降低计算成本。

**直观上来说:**

与EEMD相比，CEEMD的区别仅仅在于添加白噪声的方式上。

- EEMD添加的是相互独立的白噪声；
- CEEMD添加的是成对的、互为相反数的白噪声序列。

## 实例

为了对比残余噪声，我们分别计算使用EEMD和CEEMD方法对信号分解再重构之后的残余量

- CEEMD方法的残余辅助噪声比EEMD要低十几个数量级

![image-20250430150346283](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301505676.png) 

**实验说明**

- 在某段信号下两种方法处理后的白噪声残余随叠加次数M的变化趋势（下图），EEMD方法要在将近10000次累加之后才能将残余量降到CEEMD方法的水平，而CEEMD则在个位数的处理次数下就能达到这个水平

![image-20250430150514372](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301505677.png) 

参看: [类EMD的“信号分解方法”CEEMD](https://zhuanlan.zhihu.com/p/347783921)

## 需要调试的参数

两个

- 分别是用于平均处理的次数M
- 添加的白噪声的幅值（白噪声的幅值通常用“白噪声幅值的标准差与原始信号幅值标准差之比”来表征）。

## 优势

1.相同的累加次数下，$CEEMD$的白噪声残余更小。

2.$CEEMD$使用更少的计算资源（即更小的累加次数）即可得到理想的分解结果。
