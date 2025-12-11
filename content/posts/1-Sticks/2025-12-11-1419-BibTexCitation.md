---
slug: cd5b355cae
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
  avatar:  /images/avatar.jpeg
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
title: 参考文献引用
subtitle:
date: 2025-12-11T22:44:16+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github BugFix Screen SOP

tags:
  - SOP
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点' | '草稿'
categories:
  - '📝便签'
description: 
keywords: 
summary: 
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

**会解决的问题：**

- [x] IEEE参考文献引用，期刊、会议的缩写怎么写
- [x] 作者数量超过 6 个，怎么缩写
- [x] 期刊、会议参考文献的显示格式、设置格式

<!--more-->



## SOP | IEEE 参考文献引用 

1. Raw BibTex 
   1. IEEE 收录的文章：IEEE Xplore 
   2. 非 IEEE 收录的文章： Google Scaholar

2. 修改文献（期刊、会议的缩写）
   1. 期刊的缩写：Web of Science
   2. 会议的缩写：EI 数据库

3. 邪修：谷歌学术》》找到目标文献》》cite by 》》IEEE 系列的文章》》别人的格式

## **Raw BibTex** & 文献检索

> 寻找文章的正式发表页面，尽量少用 arxiv

- IEEE Xplore：[https://ieeexplore.ieee.org](https://ieeexplore.ieee.org/)
- 非 IEEE ：
  - GoogleScholar ① [https://scholar.google.com/](https://scholar.google.com/) ② [https://xs.xasa.top/](https://xs.xasa.top/)
  - Web of science document search : [https://webofscience.clarivate.cn/wos/woscc/basic-search](https://webofscience.clarivate.cn/wos/woscc/basic-search)
  - EI 数据库 Engineering Village - Quick Search
    [https://www.engineeringvillage.com/app/search/quick/?](https://www.engineeringvillage.com/app/search/quick/?)

----

bibtex格式的引文可以从google学术、IEEE Xplore等上获取

- IEEE论文参考文献引用格式需要期刊缩写，可以在Web of Science上查看

  直接选择：产品》》Journal Citation Reports


## **期刊缩写** 

**Web of science Journal Citation Reports :** 

[https://jcr.clarivate.com/jcr/home](https://jcr.clarivate.com/jcr/home)

**Web of science Journal** 

[https://jcr.clarivate.com/jcr/browse-journals](https://jcr.clarivate.com/jcr/browse-journals)

---



## **会议缩写**

**EI 数据库 Engineering Village - Quick Search**

[https://www.engineeringvillage.com/app/search/quick/?](https://www.engineeringvillage.com/app/search/quick/?)

SOP：conference information》》点一个文章》》查找：Abbreviated source title

## BibTex 文献格式

### 期刊

参考文献的显示格式：作者，标题，期刊名，卷号，期号，页码，==月份，年份==

```latex
[102] X. Li, “Positive-incentive noise,” IEEE Trans. Neural Netw. Learn. Syst., vol. 35, no. 6, pp. 8708–8714, Jun. 2024.
```



> <名(首字母)>. <姓（全）>, "<论文标题>," <期刊名称>, vol. <卷号>, no. <期号>, pp. <起始页码-终止页码>, <年份>.

```latex
@article{标识符,
    author  =   {{姓, 名 and 姓, 名 and ... and 姓, 名}},
    title   =   {{论文标题}},
    journal =   {{期刊名称}},
    volume  =   {卷号},
    number  =   {期号},
    pages   =   {起始页码--终止页码},
    year    =   {年份}
}

@article{RNN-1990-pollack1990recursive,
  title={Recursive distributed representations},
  author={Pollack, Jordan B},
  journal={Appl. Intell.},
  volume={46},
  number={1-2},
  pages={77--105},
  year={1990}
}
```

### 会议

参考文献的显示格式：作者，标题，会议名，==月份，年份，==卷号，期号，页码

```tex
[103] S. Huang, H. Zhang, and X. Li, “Enhance vision-language alignment with noise,” in Proc. AAAI Conf. Artif. Intell., 2025, vol. 39, no. 16, pp. 17449–17457.

[42] S. Woo et al., “ConvNeXt v2: Co-designing and scaling ConvNets with masked autoencoders,” in Proc. IEEE/CVF Conf. Comput. Vis. Pattern Recognit. (CVPR), Jun. 2023, pp. 16133–16142.
```



> <名(首字母)>. <姓（全）>, "<论文标题>," in <会议名称>, <年份>, pp. <起始页码-终止页码>

```latex
@inproceedings{标识符,
    author      =   {{姓, 名 and 姓, 名 and ... and 姓, 名}},
    title       =   {{论文标题}},
    booktitle   =   {{会议论文集名称或会议名称}},
    pages       =   {起始页码--终止页码},
    year        =   {年份}
}


@inproceedings{liu2022pyraformer,
  title={Pyraformer: Low-complexity pyramidal attention for long-range time series modeling and forecasting},
  author={Liu, Shizhan and Yu, Hang and Liao, Cong and Li, Jianguo and Lin, Weiyao and Liu, Alex X and Dustdar, Schahram},
pages={1--20},
  booktitle={Proc. Int. Conf. Learn. Representations},
  year={2021}
}
```



## IEEE 参考文献样式

- [IEEE Reference Style Guide for Authors](https://docs.google.com/document/d/1j1L96U2NagwWI9MEVDNVKt9pXxRzTH7h3krI3Mb6wZE/edit?tab=t.0#heading=h.b2e0set9htjw)



----

## 作者et al.自动缩写设置

开启IEEEtran.bst中作者et al.自动缩写功能

场景：

1. 参考文献中作者人数少于等于六个需要给出全部作者姓名，多于七个要用"第一作者+et al.(需斜体)"
1. IEEEtran.bst包含自动et al.的功能，但是默认关闭的，我们可以修改代码开启。

```latex
FUNCTION {default.is.forced.et.al} { #1 } %布尔值 
FUNCTION {default.max.num.names.before.forced.et.al} { # 6 } % 作者数量缩写阈值
FUNCTION {default.num.names.shown.with.forced.et.al} { #1 } % 缩写后显示的作者个数
```

判断逻辑，bst文件搜索：

```latex
is.forced.et.al and and

nameptr num.names.shown.with.forced.et.al
```



参考链接

- [Latex IEEE参考文献引用格式规范](https://zhuanlan.zhihu.com/p/1978440639050966153)
- IEEEtran.bst 下载地址：[https://www.bibtex.com/s/bibliography-style-ieeetran-ieeetran/#download](https://www.bibtex.com/s/bibliography-style-ieeetran-ieeetran/#download)

----

## 问题

### 🔴 参考文献的月份哪里找

- google scholar 点击 cite 的时候，先别选 bibtex ，可以看到别的引用格式给了月份



### 🔴 输出格式不对：月份，年份，卷，期，页码

> ❌ 我怎么全是这种很无语的错误啊啊啊啊啊

```latex
[2] M. Liu et al., “Scinet: Time series modeling and forecasting with sample convolution and interaction,” in Adv. Neural Inf. Proces. Syst., vol. 35, 2022, pp. 5816–5828.
```

改：

```latex
FUNCTION {inproceedings}
》》》
  publisher empty$
    { format.address.organization.date output }
    { format.organization "organization" bibinfo.check output
      format.address.publisher.date output
    }
  if$
  format.paper output
  format.series output
  format.editors output
  format.volume output
  format.number output  
  format.pages output
```

✅

```
[2] M. Liu et al., “Scinet: Time series modeling and forecasting with sample convolution and interaction,” in Adv. Neural Inf. Proces. Syst., 2022, vol. 35, pp. 5816–5828.
```






----

### 🔴 web of science那个登录的界面没有机构登录选项是怎么回事

终版解决方案 ：校园网进入网站，不要挂代理 💪🏻

你气得 我没话说

> 》》开无痕窗口

<img src="https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202512111230941.png" alt="image-20251211123013375" style="zoom:50%;" />



<!--more-->
