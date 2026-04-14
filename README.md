# Cao Ping 的技术博客

个人技术博客，基于 Jekyll + GitHub Pages 构建。

**线上地址：** [https://caoping0623.github.io](https://caoping0623.github.io)

## 本地开发

```bash
# 安装依赖
bundle install

# 启动本地服务
bundle exec jekyll serve --livereload

# 浏览器访问 http://localhost:4000
```

## 目录结构

```
├── _config.yml        # 站点配置
├── _layouts/          # 页面布局模板
├── _posts/            # 博客文章 (YYYY-MM-DD-title.md)
├── assets/css/        # 样式文件
├── index.md           # 首页
├── about.md           # 关于页
├── categories/        # 分类页
└── tags/              # 标签页
```

## 写新文章

在 `_posts/` 目录下新建文件，文件名格式：`YYYY-MM-DD-title.md`

```yaml
---
title: "文章标题"
date: 2024-04-14
categories: [分类名称]
tags: [标签1, 标签2]
excerpt: "文章摘要"
---

正文内容...
```
