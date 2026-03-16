# Hugo 重构设计文档

## 概述

将现有 Hexo 博客站点重构为 Hugo + PaperMod 主题，实现技术博客 + 作品展示的组合站点。

## 项目背景

### 当前状态
- **站点类型**: GitHub Pages 个人博客
- **当前引擎**: Hexo + NexT 主题 (Pisces 方案)
- **内容数量**: 2 篇旧文章（将被舍弃）
- **文件状态**: 只有生成的静态 HTML，无源 Markdown
- **最后更新**: 2016 年 3 月

### 重构目标
- 技术博客：分享技术文章、学习笔记
- 作品展示：展示项目、作品集

## 技术选型

| 组件 | 选择 | 理由 |
|------|------|------|
| **静态站点生成器** | Hugo Extended | 构建速度快、配置简单、社区活跃 |
| **主题** | PaperMod | 最流行(10k+ stars)、文档完善、性能最佳 |
| **主题管理** | Git Submodule | 便于更新主题版本 |
| **配置格式** | YAML | 可读性好，PaperMod 官方推荐 |
| **部署方式** | GitHub Actions | 推送源码自动构建部署 |

## 项目结构

```
wujunwei928.github.io/
├── .github/
│   └── workflows/
│       └── hugo.yml              # GitHub Actions 自动部署
├── .gitmodules                   # 主题 submodule 配置
├── archetypes/
│   └── default.md                # 文章模板
├── content/
│   ├── posts/                    # 技术博客文章
│   │   └── _index.md             # 文章列表页配置
│   ├── projects/                 # 作品展示
│   │   └── _index.md             # 项目列表页配置
│   └── about.md                  # 关于页面
├── layouts/
│   └── _default/
│       └── list.html             # 自定义列表布局（可选）
├── static/
│   └── images/                   # 图片资源
├── themes/
│   └── PaperMod/                 # Git submodule 方式引入
├── hugo.yaml                     # Hugo 配置文件
└── README.md
```

## 功能配置

### 核心功能

| 功能 | 实现方式 | 配置说明 |
|------|----------|----------|
| **深色模式** | PaperMod 原生 | 支持自动/手动切换，默认跟随系统 |
| **代码高亮** | Hugo Chroma | 内置语法高亮 + 复制按钮 |
| **文章目录 (TOC)** | PaperMod 原生 | 侧边栏显示，支持层级 |
| **搜索功能** | PaperMod Fuse.js | 全文搜索，实时响应 |
| **评论系统** | giscus | 基于 GitHub Discussions，免费无广告 |
| **RSS 订阅** | Hugo 原生 | 自动生成 RSS/Atom feed |

### giscus 配置要求
- GitHub 仓库需开启 Discussions 功能
- 需要安装 giscus GitHub App
- 仓库名称和分类名称需在配置中指定

## 部署流程

### 架构图

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  推送到 master   │ ──▶ │ GitHub Actions  │ ──▶ │  GitHub Pages   │
│  (Hugo 源码)     │     │  自动构建 Hugo   │     │  (部署站点)      │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

### 分支策略

| 分支 | 用途 |
|------|------|
| `master` | Hugo 源码（Markdown、配置、主题 submodule） |
| 部署 | GitHub Actions 直接部署到 GitHub Pages |

### GitHub Actions 配置要点
- **触发条件**: 推送到 `master` 分支
- **构建环境**: Ubuntu latest + Hugo Extended
- **Hugo 版本**: 最新稳定版
- **输出目录**: `public/`
- **部署目标**: GitHub Pages (github.io)

## 迁移步骤

### 步骤 1: 备份现有内容
- 创建 `backup` 分支保存旧站点文件
- 确保旧内容可恢复

### 步骤 2: 初始化 Hugo 项目
- 安装 Hugo Extended 版本
- 使用 `hugo new site` 创建项目结构
- 添加 PaperMod 主题为 Git Submodule

### 步骤 3: 配置站点
- 创建 `hugo.yaml` 配置文件
- 配置站点基本信息（标题、作者、URL）
- 启用所有功能（深色模式、搜索、TOC 等）
- 配置 giscus 评论系统

### 步骤 4: 创建内容模板
- 创建关于页面 (`content/about.md`)
- 创建示例文章 (`content/posts/`)
- 创建示例项目 (`content/projects/`)
- 配置导航菜单

### 步骤 5: 配置 GitHub Actions
- 创建 `.github/workflows/hugo.yml`
- 配置自动构建和部署流程
- 确保 CNAME 文件正确处理

### 步骤 6: 验证部署
- 本地运行 `hugo server` 预览
- 推送到 GitHub
- 检查 GitHub Actions 构建状态
- 验证线上站点访问

## hugo.yaml 配置概要

```yaml
baseURL: "https://wujunwei928.github.io/"
languageCode: "zh-cn"
title: "吴军伟的博客"
theme: "PaperMod"

# 导航菜单
menu:
  main:
    - name: 首页
      url: /
      weight: 1
    - name: 文章
      url: /posts/
      weight: 2
    - name: 项目
      url: /projects/
      weight: 3
    - name: 关于
      url: /about/
      weight: 4

# PaperMod 主题配置
params:
  # 深色模式
  defaultTheme: auto
  # 搜索功能
  fuseOpts: true
  # 文章目录
  toc: true
  # 代码复制按钮
  showCodeCopyButtons: true
  # 评论系统 (giscus)
  giscus:
    repo: "wujunwei928/wujunwei928.github.io"
    repoId: "<从 giscus 配置获取>"
    category: "Announcements"
    categoryId: "<从 giscus 配置获取>"

# 启用功能
outputs:
  home:
    - HTML
    - RSS
```

## 注意事项

1. **CNAME 文件**: 需要在 `static/` 目录下创建 CNAME 文件，包含自定义域名
2. **Git Submodule**: 克隆项目时需使用 `--recursive` 参数
3. **Hugo 版本**: 必须使用 Hugo Extended 版本（支持 SCSS）
4. **giscus 配置**: 需要先在 GitHub 开启 Discussions 功能

## 验收标准

- [ ] 本地 `hugo server` 可正常预览站点
- [ ] 所有功能正常工作（深色模式、搜索、TOC、代码高亮）
- [ ] GitHub Actions 构建成功
- [ ] 站点可通过 GitHub Pages 访问
- [ ] 评论系统 giscus 正常显示
- [ ] RSS 订阅链接可用
