---
slug: e9ae1ca91a
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
title: '第一篇 | 深度学习模型设计'
subtitle:
date: 2025-11-23T09:06:56+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github BugFix
tags:
  - 
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点' | '草稿'
categories:
  - '🧚🏻‍♀️深度学习'
collections:
  - '深度学习模型设计'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->
## 损失函数的设计

- 设计辅助损失是没有问题的，辅助损失加惩罚项，但是设计损失函数容易遇到的问题是梯度会断，梯度的反向传播是模型训练的基础。

## 组件设计

> 该部分着重解决的问题：已经可以理解基础组件的数据流动情况，会写伪代码，知道每个组件的作用，但是，模型性能不好。好的模型应该是根据具体的问题，提出具体的解决方案，方案要有效（性能好），方案要可落地（效率高）
>
> 效率具体来说，从以及几个方面衡量，并给出具体的指标：
>
> - parameters：参数量，表示模型的容量（capacity），一般来说参数量越多，模型的表达能力更强，容量更好
> - epoch Times：训练一次的时间，表示时间复杂度
> - FLOPs / MACs：浮点计算次数 or 乘加次数，表示计算复杂度
> - GPU Memory：空间复杂度
>
> > 后面还应该思考，这些指标都与什么有关系。比如训练时间，跟 batch_size有关，广义上来说，设计不同 batch 的不同模型进行训练时间的对比是公平的。
>
> 性能瓶颈 $\neq$ 不懂基础组件，还缺一套怎么"从指标反推回代码"的调试方法论。
>
> okay，铺垫完了，打板 action！

### 第一步：模型表达能力是否足够？

> 模型是真差还是假差

- 训练集误差不降 $\to$ ①代码写错了 ；② 梯度断了，出现梯度爆炸/梯度消失

> 🔴 回去重开吧孩子😭
>
> >  <u>在确保深刻理解问题的基础上，一般不会出现模型无效的情况；</u>
> >
> >  判断模型是否真差还有
> >
> >  ① **小样本过拟合实验** 
> >
> >  - 随机抽 50 条样本，batch=50，epoch=200，看训练损失是否$→0$，accuracy$→100 \%$
> >  - 若不过拟合 → 代码有 bug（梯度断、loss 写错、mask 漏）
> >
> >  ② **关掉所有正则**（dropout=0, weight_decay=0），确认 "baseline 能上 $90\%$ "
> >
> >  应该有的产出：一张 loss $ \neq 0$ 的曲线截图，证明 "模型无硬伤"

- 训练集误差下降，验证集误差上升 $\to$  过拟合，见第二步

> 训练集误差下降，说明模型是有效的，但是过拟合

- 训练集误差下降，验证集误差下降 $\to$ 欠拟合，见第二步

> 模型有表达能力，但是欠拟合，此时需要思考模型收敛的边界在哪里？调超参数能带来的收益有多少？是增加模型的宽度还是深度？

### 第二步：如何充分发挥模型的表达能力？

> 既然模型有足够的容量，那么应该如何充分发挥模型的表达能力。

- 训练集误差 $<<$ 验证集误差，且gap 随着 epoch 继续拉大 $\to$ 过拟合

> 🟢 策略：此时，可采取的方法：① dropout $\uparrow$  ② 学习率 $\uparrow$ ③设置早停 ④权重衰减 $\uparrow$ 
>
> 🟡 观察：gap 缩小

- 训练集误差 $\approx$ 验证集误差，并且都很高 $\to$ 模型表达能力不够

> 🟢 策略：
>
> - 此时，可采取 ① 调超参数 ② 增加模型深度 ③增加模型深度 
>
> - 优先增加深度和宽度，可以显著提高模型的表达能力，超参数带来的收益相对有限
> - 具体来说，隐藏层维度 $\times 2$ ；增加隐藏层 
>
> 🟡 观察：训练集误差、验证集误差都有下降

### 第三步：调超参数

> 我非常很好奇，调超参数能释放多少模型性能？
>
> 「
>
> ​	在深度学习领域，专家和新手用着表面上类似的方法，但所取得的结果之间存在着巨大的差距。
>
> 」——《[深度学习调参指南](https://github.com/schrodingercatss/tuning_playbook_zh_cn)》



（1）学习率 & batch_size 

使用场景：

- 训练集误差振荡不下降 $\to$ 学习率太大
- 训练集误差下降非常慢 $\to$ 学习率太大 or batch 太大

> 调超参数可视化： 3D 散点图（lr, batch, hidden_dim）

？？？解决策略：

```
# 3 行代码跑 LR-range-test
optimizer = torch.optim.AdamW(model.parameters(), lr=1e-7)
scheduler = torch.optim.lr_scheduler.ExponentialLR(optimizer, gamma=1.15)
# 每 50 step 记录 loss，画 log-log 曲线
```

（2）这部分需要补充：还有优化器、初始化方法等等，深度学习优化好像是一门专门的学问

| 模块   | 默认                | 代码                                                         |
| ------ | ------------------- | ------------------------------------------------------------ |
| 优化器 | AdamW               | `torch.optim.AdamW(model.parameters(), lr=3e-4, weight_decay=0.05)` |
| 调度器 | CosineLR + Warmup   | `get_cosine_schedule_with_warmup(optimizer, num_warmup_steps=total_steps*0.1, num_training_steps=total_steps)` |
| 初始化 | Xavier/He           | `for m in model: if isinstance(m, nn.Linear): nn.init.xavier_uniform_(m.weight)` |
| 正则   | Label Smoothing 0.1 | `nn.CrossEntropyLoss(label_smoothing=0.1)`                   |
| 精度   | Mixed Precision     | `torch.cuda.amp.autocast()` + `GradScaler()`                 |

### 第四步：思考模型本身的设计

> inductive bias 归纳偏置是否充分考虑，这个需要充分理解任务本身，也是模型设计的起点

| 现象             | 缺什么偏置 | 一键补丁                                    |
| ---------------- | ---------- | ------------------------------------------- |
| 长周期预测误差大 | 无多尺度   | 加 U-Net/金字塔池化                         |
| 季节性相位错     | 无显式周期 | STL 分解 or 傅里叶项                        |
| 短突变抓不住     | 感受野小   | Dilated Conv / 滑动步长=1 的 Transformer    |
| 变量间耦合弱     | 独立通道   | Cross-Dimension Attention（Crossformer 式） |

策略：

- 每次直接一个偏置模块

- 验证：MSE 变化 $1\%$ 就保留

最后的最后，没有办法的办法：集成学习。
