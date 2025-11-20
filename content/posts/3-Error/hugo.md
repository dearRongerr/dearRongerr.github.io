---
date: '2025-05-01T22:29:11+08:00'
lastmod: '2025-05-01T22:29:11+08:00'
draft: false
title: 'Hugo 公式渲染问题'
math: true
# 'Hugo'  ,  'Python'  ,  'DeepLearning'  ,  'Statistics','VSCode'
# categories: ['Hugo']
# series: [""]  # 添加此行，用于系列文章
# series_order: 1  # 添加此行，用于指定文章在系列中的顺序
# '注意力机制'  ,  '入门'  ,  'Sticks':注意事项,  'TODO'
# categories: ['Hugo']
toc: true
categories:
  - '🕷️捉个虫'
tags:
  - Hugo
---

🟢 场景描述: hugo -Stack 主题渲染公式不正常,Typora 渲染正常

🔵 原因分析: 

- Typora 使用 MathJax渲染

-  Hugo 渲染效果更接近 KaTeX

## 测试

问题详细描述:

- (全部正常)test : 反斜杠转义下划线

  	 $\mathcal{X}\_{\text{final}} = \mathcal{X}\_{\text{trend}} + \mathcal{X}_{\text{de}}^M$
	

​	$X_{future\_new}$

- 问题 2: 同样的问题, 下面的公式也是,行内公式hugo 渲染不正常,单行公式可正常渲染

$\mathcal{X}_{\text{final}} = \mathcal{X}_{\text{trend}} + \mathcal{X}^M_{\text{de}}$
$$
\mathcal{X}_{\text{final}} = \mathcal{X}_{\text{trend}} + \mathcal{X}^M_{\text{de}}
$$
$\mathcal{X}_\text{{finall}}=\mathcal{X}_\text{trend}$

$\mathcal{X}_\text{{finall}}$

$\mathcal{X}_\text{trend}$ 

$\mathcal{X}^M_{\text{de}}$ 

经过反复的测试,问题出在,两个 `_` 一起在行内公式中出现时,hugo 今晚不能渲染了

- 问题 1 : $行内公式有\text{Padding}(x_{seasonal}) $   
- 行内不正常渲染,但是单行公式一直是正常渲染的
- **解决 1** 先写上标,再写下标

```latex
\mathcal{X}^0_{\text{de}} = \text{Padding}(\mathbf{x}_{\text{seasonal}})
```

$\mathcal{X}^0_{\text{de}} = \text{Padding}(\mathbf{x}_{\text{seasonal}})$
$$
\mathcal{X}_{\text{de}}^0 = \text{Padding}(\mathbf{x}_{\text{seasonal}})
$$
简单行内公式: $E=mc^2$

一般公式: $\mathcal{X}_{\alpha} = \frac{1}{\sqrt{2\pi}}$

复杂公式:

$$
\begin{align}
\nabla \times \vec{\mathbf{B}} -\, \frac1c\, \frac{\partial\vec{\mathbf{E}}}{\partial t} & = \frac{4\pi}{c}\vec{\mathbf{j}} \\
\nabla \cdot \vec{\mathbf{E}} & = 4 \pi \rho \\
\nabla \times \vec{\mathbf{E}}\, +\, \frac1c\, \frac{\partial\vec{\mathbf{B}}}{\partial t} & = \vec{\mathbf{0}} \\
\nabla \cdot \vec{\mathbf{B}} & = 0
\end{align}
$$

## 最终的解决方法

hugo.yaml 中,缺少行内公式的配置,补上即可

```yaml
markup:
    goldmark:
        extensions:
            passthrough:
                enable: true
                delimiters:
                    block:
                        - - \[
                          - \]
                        - - $$
                          - $$
                    inline:
                        - - \(
                          - \)
                        - - $
                          - $
```

