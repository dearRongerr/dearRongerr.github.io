---
date: '2025-04-29T22:48:19+08:00'
lastmod: '2025-04-29T22:48:19+08:00'
draft: false
title: '序列分解应用篇'
math: true
# 'Hugo'  ,  'Python'  ,  'DeepLearning'  ,  'Statistics','VSCode'
# categories: ['TimeSeries']
# # series: [""]  # 添加此行，用于系列文章
# # series_order: 1  # 添加此行，用于指定文章在系列中的顺序
# # '注意力机制'  ,  '入门'  ,  'Sticks':注意事项,  'TODO'
# tags: ['实际应用',"例子"]
toc: true
categories:
  - '🖇️碎笔论文'
---

reference :[EMD 基础理论篇](https://zhuanlan.zhihu.com/p/40005057)

[EMD 例题](https://pan.baidu.com/s/1O2NwWHElGgpgPZlgSd5bxQ?pwd=qa6q)

## 基础理论

EMD 的通俗理解:

> 对于一段未知信号，不需要做预先分析与研究，就可以直接开始分解。这个方法会自动按照一些固模式按层次分好，而不需要人为设置和干预。
>
> 再通俗一点，EMD就像一台机器，把一堆混在一起的硬币扔进去，他会自动按照1元、5毛、1毛、5分、1分地分成几份。
>
> (有点像傅里叶变换呀)

## 内生模态分量

（Intrinsic Mode Functions, IMF）

why?

- 任何信号都可以拆分成若干个内涵模态分量之和
- 需要满足两个条件:

(1)在整个数据段内，极值点的个数和过零点的个数必须相等或相差最多不能超过一个,通俗理解:

- 图线要反复跨越x轴

![image-20250429225833625](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301025629.png) 

- ❌ 而不能像这样某次穿过零点后出现多个极点：

![image-20250429225930599](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301025631.png) 

(2)在任意时刻，由局部极大值点形成的上包络线和由局部极小值点形成的下包络线的平均值为零，即上、下包络线相对于时间轴局部对称。通俗理解: 

![image-20250429225955324](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301025632.png) 

- ❌ 包络线不对称

![image-20250429230039768](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301025633.png) 

## EMD 例子

(1)根据原始信号上下极值点，分别画出上、下包络线

(2)求上、下包络线的均值，画出均值包络线

(3)原始信号减均值包络线，得到中间信号

![image-20250429230212993](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301025634.png)

(4)判断该中间信号是否满足IMF的两个条件，如果满足，该信号就是一个IMF分量；如果不是，以该信号为基础，重新做(1)~(4)的分析。IMF分量的获取通常需要若干次的迭代 

这里不满足(2): 包络线对称

![image-20250429230648413](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301025635.png)

所以继续迭代:

![image-20250429230807184](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301025636.png) 

直到 : 

![image-20250429230847848](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301025637.png) 

满足(1)极值&过零点条件 (2)包络线对称 

继续分解 IMF2,原始信号减去 IMF1,继续分解

## IMF 物理含义

举例:假设生成一个信号,4Hz的正弦波、10Hz的正弦波和白噪声叠加而成的,进行 EMD 分解,可以得到下图：

![image-20250430101215523](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301025638.png)   

解读: IMF1为10Hz的分量，IMF2为4Hz的分量;

IMF的各个分量分别代表了原始信号中的各频率分量，并按照从高频到低频的顺序依次排列

> 问题:IMF3分量又代表了什么？
>
> 属于EMD端点效应带来的副作用

🚩 **EMD的一大特点**：自适应地进行信号主要成分分析（不是PCA）。也就是说，EMD分解信号不需要事先预定或强制给定基函数，而是依赖信号本身特征而自适应地进行分解。(区别于傅里叶变换,使用正交基函数)

🚩 **EMD 的用处**:  现实中的信号分量（IMF）不会像例子中一样保持完全稳定的频率和振幅，也常常无法从各分量中直接看出信号规律。EMD分解经常被用作信号特征提取的一个预先处理手段，将各IMF分量作为后续分析方法的输入，以完成更加复杂的工作

## 希尔伯特-黄变换（HHT）

from: [希尔伯特-黄变换（HHT）的前世今生——一个从瞬时频率讲起的故事](https://zhuanlan.zhihu.com/p/124257081)

- 全称: HHT，Hilbert-Huang Transform
- 定义: 经过EMD分解出的IMF分量再经过Hilbert变换，最终得到信号瞬时频率和瞬时幅值
- 用处: 

(1) HHT的结果反映的是信号的时频特征，即信号的频域特征随时间变化的规律。相对于傅里叶变化得到的是信号的频率组成，HHT还可以获取频率成分随时间的“变化”。比如我们要分析的信号代表的是一个性能迅速退化的发动机（假设信号表征是某些IMF分量的频率逐渐升高），使用HHT就可以对该现象进行很好的捕捉。

(2) HHT可以对局部特征进行反映，这点主要得益于EMD的作用。EMD可以自适应地进行时频局部化分析，有效提取原信号的特征信息。

(3) “分解”往往可以对应着“重构”，从HHT结果中选择出满足要求的特征分量并重组信号，有利于将关注的特征从复杂的混合信号中分离出来。

由于这些优点，HHT方法与短时傅里叶变换（STFT）和小波变换（wavelet）等方法共同成为了时频域分析界的重要手段。

📝 **生动形象的例子:** 实数轴,虚数轴,时间轴

![image-20250430102927171](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301056692.png)

这里的数学表达式:

对于信号 $x(t)$  故事中的小 X(实数轴) ,其解析信号(故事中的小 A)的表达式:
$$
a(t)= x(t)+jy(t)
$$

瞬间幅值表达式:
$$
A(t)=\sqrt{x^2(t)+y^2(t)}
$$
瞬间相位表达式:
$$
\theta(t)= arctan\frac{y(t)}{x(t)}
$$
瞬间频率表达式:
$$
\omega(t) = \frac{d\theta(t)}{dt}
$$
**生动形象的例子**: 

![image-20250430103133798](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202504301056693.png) 

1. 所谓的疯狂的M（非平稳信号），不过是几个普通人（平稳信号）叠罗汉的恶作剧罢了。
2. 只要使用EMD就可以把他们分离开。
3. 不如把被分离开的这几个家伙叫做IMF（内涵模态分量 Intrinsic Mode Functions）吧。
4. 此时再对这几个IMF分量求瞬时频率就没大问题了

> **为什么要求 瞬间频率**  

