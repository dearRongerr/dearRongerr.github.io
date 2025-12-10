---
date: 2025-04-28T10:20:40+08:00
lastmod: 2025-04-28T10:20:40+08:00
draft: false
title: Matplotlib科研绘图设置
math: true
toc: true
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
categories:
  - '⛄好用的代码片'
tags:
  - Matplotlib
---

## 导入库

```python
import matplotlib.font_manager as fm
import matplotlib.pyplot as plt
```



## 字号

14(标题)-12(xy 轴标签字号)-10(xy 轴刻度字号,图例)

```Python
plt.rcParams['font.size'] = 12  # 设置全局字体大小
plt.rcParams['axes.titlesize'] = 14  # 标题字体大小
plt.rcParams['axes.labelsize'] = 12  # 坐标轴标签字体大小
plt.rcParams['xtick.labelsize'] = 10  # x轴刻度标签字体大小
plt.rcParams['ytick.labelsize'] = 10  # y轴刻度标签字体大小
plt.rcParams['legend.fontsize'] = 10  # 图例字体大小
plt.rcParams['figure.dpi'] = 300  # 设置分辨率
```
## 设置刻度朝向

```python
plt.rcParams['xtick.direction'] = 'in'
plt.rcParams['ytick.direction'] = 'in'
```

## 设置中文字体

本地中文字体注册：

```bash
/System/Library/Fonts/Supplemental/Songti.ttc
```

- 存的字体文件
  - 链接: [https://pan.baidu.com/s/1N2LN_jhiADVNViR2jlpzCg?pwd=htsk](https://pan.baidu.com/s/1N2LN_jhiADVNViR2jlpzCg?pwd=htsk) 提取码: htsk
- matplotlib+MacOS 中英文字体混排的坑
  - 首先，MacOS 上的 Songti.ttc是加粗的
  - 其次，Simsun.ttc才是常规的宋体
  - 这里有：字号设置不生效（因为设置了全局字体）、中文字体自动加粗（因为是Songti.ttc）的矛盾
  - 解决：弄个 `Simsun.ttc` 就好

> 这里给到的建议就是，直接都用英文就好了。没有强制要求中文，又何必请求，麻烦而且不好看。



```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.font_manager as fm

#  绑定系统宋体
fm.fontManager.addfont('/System/Library/Fonts/Supplemental/Songti.ttc')
plt.rcParams['font.family'] = 'Songti SC'        # 中文
plt.rcParams['mathtext.fontset'] = 'stix'        # 公式英文
plt.rcParams['axes.unicode_minus'] = False       # 负号
```


## 紧凑布局

```python
plt.tight_layout() # 紧凑布局
```

## 字体

全英文字体,新罗马

```python
# 字体设置
font_path = 'fonts/times.ttf'
bold_font_path = 'fonts/timesbd.ttf'
font_prop = fm.FontProperties(fname=font_path)
bold_font_prop = fm.FontProperties(fname=bold_font_path)
```

## 画布大小

(1)单栏图表：宽度为 8-9 厘米（3.15-3.54 英寸），高度根据内容调整，通常为 5-7 厘米。

(2)双栏图表：宽度为 16-18 厘米（6.3-7.09 英寸），高度根据内容调整，通常为 10-15 厘米。

一般在 matplotlib 中,直接:

```Python
fig = plt.figure(figsize=(10, 6))
```

## 颜色

我现在用的:

```Python
# 定义颜色
light_blue = '#4682B4'   # 钢蓝色(主色调)
light_red = '#ff7688'    # 亮红色
light_yellow = '#FFA500' # 橙色
light_green = '#2E8B57'  # 海洋绿
```

另一组:

```Python
colors = ['#0072B2', '#E69F00', '#009E73', '#D55E00', '#CC79A7', '#F0E442', '#999999', '#000000']
```

## 网格线

透明度,线宽,线条样式,颜色

```Python
ax.grid(color='#cccccc', linestyle='--', linewidth=0.5, alpha=0.7)
```

## 刻度

(1) 刻度线方向朝里朝外都可以

(2) 设置xy 轴刻度 紧挨着 坐标轴

(3)保留完整的四周线

```
# 刻度线方向朝外，刻度标签紧挨着刻度轴
# xy 轴标签距离刻度轴也紧凑一些，使得标题，xy 轴标签，xy 轴刻度轴更有层次感
# 多个子图设置标题标签(a)(b)(c)，设置在图片下方
# 一张图直接加标题即可
```

## 清除缓存

```Python
# 正式画图前 清除 matplotlib 缓存
mpl.rcParams.update(mpl.rcParamsDefault)  # 重置所有参数
plt.close('all')  # 关闭所有图表
```

## 图例

```python
ax.legend(loc='upper right')
```

## 线宽

电子屏幕演示 : `linewidth=1`

打印: `linewidth=2`

## 标记

```Python
marketsize = 2.5 # 标记的大小
```

## 保存设置

(1)保存图片: pdf 格式,dpi=300 即可

```python
acf_pacf_path = os.path.join(output_dir, 'acf_pacf_analysis.pdf')
plt.savefig(acf_pacf_path,, bbox_inches='tight', dpi=300)
```

- `bbox_inches='tight'`保存图去掉白边

(2 )图片保留目录,并保存必要的日志(关键数据)

```python
# 文件目录
log_dir = 'logs/electricity'
output_dir = 'images/electricity/window'
data_path = 'dataset/electricity/electricity.csv'
raw_data_path=''
```

## 字号与画布大小的平衡

- 字号与画布大小的平衡

> 为什么这么说?
>
> (1)首先,如何设置合适的画布大小,在自己的论文中不需要缩放,字号显示也是刚刚好,但是在自己的画图的时候,后台画图时会束手束脚的,因为画布太小了,而且 latex 就是会缩放
>
> (2)如果后台画图舒适,那你放到论文中,就会缩放,字号就会变小,而且在缩放的话,能占更多的页面空间

当前 latex 中,插入图片的设置:

```Python
\begin{figure}[hbt!]
    \centering
    \includegraphics[width=1\textwidth]{figures/1.pdf}
    \caption{聚类评估}
    \label{fig:cluster_analysis_evaluation}
\end{figure}
\section{特征分析}
```

## 字号对应关系

```python
# 设置全局字体大小和样式
plt.rcParams['font.size'] = 10  # 全局字体大小，相当于五号字
plt.rcParams['axes.titlesize'] = 12  # 标题字体大小，相当于小四号字
plt.rcParams['axes.labelsize'] = 10  # 坐标轴标签字体大小，相当于五号字
plt.rcParams['xtick.labelsize'] = 8  # x轴刻度标签字体大小，相当于小五号字
plt.rcParams['ytick.labelsize'] = 8  # y轴刻度标签字体大小，相当于小五号字
plt.rcParams['legend.fontsize'] = 8  # 图例字体大小，相当于小五号字
plt.rcParams['figure.dpi'] = 300  # 设置分辨率
plt.rcParams['font.family'] = font_prop.get_name()  # 设置全局字体
```

## 完整的设置

也就是丢给 Ai 前.先告诉它作图规范

## 目前使用的设置

mark 一下

- 字号: `16-14-10` 标题-标签-刻度,图例
- 字体: `TimesNewRoman粗体-TimesNewRoman粗体-TimesNewRoman常规体`
- 紧凑排列: `plt.tight_layout()`

显示效果, `2×1` 的图片 或者 `1×1` 的图片排版都挺好看的

这里的考虑:

- 10×6 的画布大小和 A4 纸差不多
- 字号变大 2,再缩放,影响就不大了

看点代码:

```python
# 字体设置
font_path = 'fonts/times.ttf'  # Times New Roman 常规字体
bold_font_path = 'fonts/timesbd.ttf'  # Times New Roman 粗体字体
font_prop = fm.FontProperties(fname=font_path)
bold_font_prop = fm.FontProperties(fname=bold_font_path)
```

使用:

```python
# 绘制ACF和PACF图
plt.figure(figsize=(10, 6))

# ACF图
plt.subplot(2, 1, 1)
plt.bar(range(len(lag_acf)), lag_acf, width=0.3, color=light_blue)
plt.axhline(y=0, linestyle='-', color='black')
plt.axhline(y=confidence, linestyle='--', color=light_red)
plt.axhline(y=-confidence, linestyle='--', color=light_red)
plt.title('Autocorrelation Function (ACF)', fontproperties=bold_font_prop, fontsize=16)
plt.xlabel('Lag', fontproperties=bold_font_prop, fontsize=14)
plt.ylabel('Correlation', fontproperties=bold_font_prop, fontsize=14)
plt.xticks(fontproperties=font_prop)
plt.yticks(fontproperties=font_prop)
plt.grid(True, linestyle='--', alpha=0.5)

# PACF图
plt.subplot(2, 1, 2)
plt.bar(range(len(lag_pacf)), lag_pacf, width=0.3, color=light_green)
plt.axhline(y=0, linestyle='-', color='black')
plt.axhline(y=confidence, linestyle='--', color=light_red)
plt.axhline(y=-confidence, linestyle='--', color=light_red)
plt.title('Partial Autocorrelation Function (PACF)', fontproperties=bold_font_prop, fontsize=16)
plt.xlabel('Lag', fontproperties=bold_font_prop, fontsize=14)
plt.ylabel('Correlation', fontproperties=bold_font_prop, fontsize=14)
plt.xticks(fontproperties=font_prop)
plt.yticks(fontproperties=font_prop)
plt.grid(True, linestyle='--', alpha=0.5)

plt.tight_layout()
acf_pacf_path = os.path.join(output_dir, 'acf_pacf_analysis.pdf')
plt.savefig(acf_pacf_path, dpi=300)
print(f"自相关分析图表已保存至: {acf_pacf_path}")

```

值得注意的是:

- 字号,字体

```Python
plt.title('Partial Autocorrelation Function (PACF)', fontproperties=bold_font_prop, fontsize=16)
plt.xlabel('Lag', fontproperties=bold_font_prop, fontsize=14)
plt.ylabel('Correlation', fontproperties=bold_font_prop, fontsize=14)
plt.xticks(fontproperties=font_prop)
plt.yticks(fontproperties=font_prop)
```

- 标签:粗体
- 刻度: 常规体

### 当前设置

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import matplotlib.dates as mdates
import os
from matplotlib.gridspec import GridSpec
import matplotlib.font_manager as fm
import matplotlib as mpl

# 文件设置
output_dir = 'images/electricity/window'
cleaned_file_path = 'dataset/electricity/electricity.csv'  # 添加数据文件路径
os.makedirs(output_dir, exist_ok=True)

# ====画图设置====
# 定义颜色
light_blue = '#4682B4'   # 钢蓝色(主色调)
light_red = '#ff7688'    # 亮红色
light_yellow = '#FFA500' # 橙色
light_green = '#2E8B57'  # 海洋绿

# 字体设置
font_path = 'fonts/times.ttf'
bold_font_path = 'fonts/timesbd.ttf'
font_prop = fm.FontProperties(fname=font_path) # 标题,xy 轴标签字体
bold_font_prop = fm.FontProperties(fname=bold_font_path) # xy 轴刻度字体 

# 注册自定义字体
font_files = [font_path, bold_font_path]
for font_file in font_files:
    mpl.font_manager.fontManager.addfont(font_file)

# 设置全局字体为Times New Roman
plt.rcParams['font.family'] = 'Times New Roman'
plt.rcParams['font.size'] = 12 # 设置全局字体大小
plt.rcParams['axes.titlesize'] = 16 # 标题字体大小
plt.rcParams['axes.labelsize'] = 14 # 坐标轴标签字体大小
plt.rcParams['xtick.labelsize'] = 10 # x轴刻度标签字体大小
plt.rcParams['ytick.labelsize'] = 10# y轴刻度标签字体大小
plt.rcParams['legend.fontsize'] = 10 # 图例字体大小
plt.rcParams['figure.dpi'] = 300  # 设置分辨率
plt.tight_layout() # 紧凑布局
plt.grid(True, color='#cccccc',linestyle='--', alpha=0.7)  # 网格线设置
fig = plt.figure(figsize=(10, 6)) # 画布大小,必须
```

📝 说明: 直接复制,设置的格式是

- 标题:

> - Times New Roman 粗体 (bold_font_prop，来自'fonts/timesbd.ttf')
> - 字号: 16点 (由全局设置 `plt.rcParams['axes.titlesize'] = 16` 控制)

- **X轴 , Y 轴标签: **

> - 字体: Times New Roman 粗体 (bold_font_prop，来自'fonts/timesbd.ttf')
>
> - 字号: 14点 (由全局设置 plt.rcParams['axes.labelsize'] = 14 控制)

- **X轴 , Y 轴刻度,图例: **

> - 字体: Times New Roman (由全局设置 `plt.rcParams['font.family']` 控制)
>
> - 字号: 10点 (由全局设置 `plt.rcParams['ytick.labelsize'] = 10` 控制)

- 标记,线宽: `marker='o', markersize=2.5, linewidth=1` 

### 遇到的问题

> Matplotlib中常见的行为：全局设置的plt.rcParams['axes.titlesize'] = 16没有正确应用到所有标题上，但显式指定fontsize=16时却生效了。这是因为：
>
> ```tex
> FontProperties优先级高于rcParams
> ```

咋办: 遍历所有子图,设置

```Python
# 在所有图表绘制完成后
for ax in axes.flatten():
    # 设置标题
    ax.title.set_fontproperties(bold_font_prop)
    ax.title.set_fontsize(16)
    
    # 设置x轴和y轴标签字体
    ax.xaxis.label.set_fontproperties(bold_font_prop)
    ax.yaxis.label.set_fontproperties(bold_font_prop)
    
    # 设置x轴和y轴标签字号
    ax.xaxis.label.set_fontsize(14)
    ax.yaxis.label.set_fontsize(14)
    
    # 设置x轴和y轴刻度标签字体
    for label in ax.get_xticklabels():
        label.set_fontproperties(font_prop)
        
    for label in ax.get_yticklabels():
        label.set_fontproperties(font_prop)
    
    # 设置x轴和y轴刻度标签字号
    ax.tick_params(axis='x', labelsize=10)
    ax.tick_params(axis='y', labelsize=10)
```



## 常用的设置,直接复制

```python
marker='o',markersize=2.5,linewidth=1
fontproperties=bold_font_prop, fontsize=16
```

