# Bernard's Blog

基于 [Hugo](https://gohugo.io/) + [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 的个人博客，线上地址：<https://bernard035.github.io/>。

## 内容结构

```
content/posts/          # 全部文章（英文 kebab-case 文件名）
  ├── 算法竞赛           # 212 篇：题解 / 赛后分析 / 模板 / 题集 / 学习笔记（整理自牛客博客）
  └── 技术分享           # 性能工程 / AI 工程 / 数据库等技术文章
static/images/          # 文章配图（自托管，相对引用 /images/...）
layouts/partials/extend_head.html   # KaTeX 公式渲染（仅对 math: true 的页面加载）
```

分类体系：**【算法竞赛】** 与 **【技术分享】** 两类，见 `/categories/`。

## 写新文章

新建 `content/posts/<slug>.md`，front matter 模板：

```yaml
---
title: "文章标题"
date: 2026-09-08T12:00:00+08:00
draft: false
math: true            # 仅当含 LaTeX 公式时需要（$...$ 行内 / $$...$$ 独立公式）
showToc: true
categories: ["技术分享"]   # 或 "算法竞赛"
tags: ["Hugo", "性能优化"]
---
```

## 预览 / 构建

```sh
hugo server -D   # 本地预览（需 hugo extended）
hugo             # 构建到 public/（已被 gitignore）
```

## 部署

全自动：push 到 `main` 后，GitHub Actions（`.github/workflows/gh-pages.yml`）
自动构建并发布到 `bernard035/bernard035.github.io` 仓库（secret: `PUBLISH_IO_TOKEN`）。

> 网络不通时走本地代理推送：
> `git -c http.proxy=http://127.0.0.1:7890 -c https.proxy=http://127.0.0.1:7890 push origin main`
