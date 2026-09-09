+++
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
date: <% tp.date.now("YYYY-MM-DDTHH:mm:ss+08:00") %>
description = "在这里填写文章描述，将显示在搜索结果和预览中"
draft = true

# 文章分类和标签
tags = []
categories = []

# 封面图片（PaperMod 专属规范嵌套结构）
[cover]
image = ""          # 图片路径，如 "images/cover.jpg"
alt = "封面图片描述"
caption = "图片来源"
relative = false

# 页面显示选项
ShowToc = true           # 显示目录
TocOpen = false          # 目录是否默认展开
ShowReadingTime = true   # 显示阅读时间
ShowPostNavLinks = true  # 显示上一篇/下一篇
ShowCodeCopyButtons = true # 显示代码复制按钮
ShowWordCount = true     # 显示字数统计

# 元数据
author = "作者名"
canonicalURL = ""
weight = 0
+++

## 概述

用一两句话总结这篇文章的核心内容。

## 背景

介绍问题的背景和为什么需要写这篇文章。

## 核心内容

### 部分一
具体内容...

### 部分二
具体内容...

## 代码示例

```python
# 代码示例
print("Hello World")

## 关键要点

- 要点 1
- 要点 2
- 要点 3

## 参考资源

- [参考链接1](https://example.com)
- [参考链接2](https://example.com)

## 总结

总结本文的核心观点和实践价值。