---
date: 2025-05-05T16:55:49+08:00
draft: false
title: "Hugo + Obsidian + QuickAdd "
math: true
# tags: ["Hugo"]
# series: ["Hugo + Obsidian"]
series_order: 1       # 文章在系列中的顺序
toc: true
categories:
  - '📝便签'
---

这是 Hugo+Obsidian 的第一篇

> 动机: 重构了 Hugo 的目录结构, 分了很多类别, Hugo 新建文件需要置顶元数据, typora不能满足这个功能, 命令行窗口那么多文件夹也记不住,so ,Obsidian ε＝ε＝ε＝(#>д<)ﾉ

1. 安装Obsidian (百度即可直接安装)
2. 安装 Obsidian 插件

① 第三方插件,关闭安全模式

②搜索插件并安装: `(1)` Git `(2)` Image auto upload (搜索 PicGo 找到 ) `(3)` Quick Add

## Quick Add

① 新建文件: `hugo-site>>archetypes>>hugo-posts.md` 中填写内容

```markdown
---
date: {{date:YYYY-MM-DD}}T{{time:HH:mm:ss}}+08:00
draft: false
title: " "
math: true
# categories: [" "]
toc: true
---
```

②依次点击 ⚙ >> 第三方插件>>QuickAdd>>Manage Macrocs >> name 填写 `hugo-posts` (optional) >> Template(表示这是模版文件) >> Add Choice

③ QuickAdd Settings 出现 `hugo-posts` , 接下来依次点击 >> 小闪电 (方便后面 command+P 直接输入 hugo 即可选择该模版文件) >> 设置 ⚙ >> 

④ 进入 hugo-posts 设置 >> Template Path (选择 `archetypes>>hugo-posts.md`文件路径 ) >> 打开 Create in folder  (用于控制在哪个文件夹中使用该模版) >> 设置路径 `content/post`

### 逐步实操

1. 打开 Obsidian
2. 键入 command + P, 输入 'hugo', 选择 `QuickAdd:hugo-posts`
3. 选择在哪个文件夹下新建 .md 文件
4. 键入文件名称,建好了文件就写吧



