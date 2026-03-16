# Hugo 重构实施计划

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将现有 Hexo 博客站点重构为 Hugo + PaperMod 主题，实现技术博客 + 作品展示站点。

**Architecture:** 使用 Hugo Extended 静态站点生成器 + PaperMod 主题（Git Submodule 管理），通过 GitHub Actions 自动构建部署到 GitHub Pages。

**Tech Stack:** Hugo Extended, PaperMod 主题, YAML 配置, GitHub Actions, giscus 评论

---

## 文件结构

| 文件路径 | 操作 | 说明 |
|----------|------|------|
| `.github/workflows/hugo.yml` | 创建 | GitHub Actions 自动部署配置 |
| `.gitmodules` | 创建 | 主题 submodule 配置 |
| `archetypes/default.md` | 创建 | 文章模板 |
| `content/posts/_index.md` | 创建 | 文章列表页配置 |
| `content/projects/_index.md` | 创建 | 项目列表页配置 |
| `content/about.md` | 创建 | 关于页面 |
| `static/CNAME` | 保留 | 自定义域名配置 |
| `themes/PaperMod/` | 创建 | Git Submodule |
| `hugo.yaml` | 创建 | Hugo 主配置文件 |
| `README.md` | 创建 | 项目说明 |

---

## Chunk 1: 备份与清理

### Task 1.1: 创建备份分支

**Files:**
- N/A（Git 操作）

- [ ] **Step 1: 创建备份分支保存旧站点**

```bash
git checkout -b backup/hexo-site
git push origin backup/hexo-site
```

Expected: 分支创建成功并推送到远程

- [ ] **Step 2: 切回 master 分支**

```bash
git checkout master
```

Expected: 已切换到 master 分支

---

### Task 1.2: 清理旧文件

**Files:**
- 删除: `2016/`, `archives/`, `css/`, `fancybox/`, `images/`, `js/`, `vendors/`, `index.html`

- [ ] **Step 1: 删除旧站点文件**

```bash
rm -rf 2016 archives css fancybox images js vendors index.html
```

Expected: 旧文件已删除

- [ ] **Step 2: 保留 CNAME 文件**

```bash
ls CNAME
```

Expected: CNAME 文件存在

- [ ] **Step 3: 提交清理变更**

```bash
git add -A
git commit -m "chore: 清理旧 Hexo 站点文件"
```

Expected: 变更已提交

---

## Chunk 2: Hugo 项目初始化

### Task 2.1: 检查 Hugo 安装

**Files:**
- N/A

- [ ] **Step 1: 检查 Hugo 是否已安装**

```bash
hugo version
```

Expected: 显示 Hugo 版本（需要 Extended 版本）

- [ ] **Step 2: 如未安装，安装 Hugo Extended**

```bash
# Ubuntu/Debian
sudo apt install hugo

# 或使用 snap（确保是 extended 版本）
sudo snap install hugo --channel=extended
```

Expected: Hugo 安装成功

---

### Task 2.2: 创建 Hugo 项目结构

**Files:**
- 创建: `archetypes/default.md`
- 创建: `content/posts/`
- 创建: `content/projects/`
- 创建: `layouts/`
- 创建: `static/`

- [ ] **Step 1: 创建必要目录**

```bash
mkdir -p archetypes content/posts content/projects layouts/_default static/images
```

Expected: 目录创建成功

- [ ] **Step 2: 创建默认文章模板**

创建文件 `archetypes/default.md`:

```markdown
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true
description: ""
tags: []
categories: []
---

```

- [ ] **Step 3: 提交项目结构**

```bash
git add archetypes/
git commit -m "feat: 创建 Hugo 项目结构"
```

Expected: 变更已提交

---

### Task 2.3: 添加 PaperMod 主题

**Files:**
- 创建: `.gitmodules`
- 创建: `themes/PaperMod/` (submodule)

- [ ] **Step 1: 添加 PaperMod 为 Git Submodule**

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

Expected: 主题 submodule 添加成功

- [ ] **Step 2: 验证 submodule 配置**

查看 `.gitmodules` 文件内容:

```ini
[submodule "themes/PaperMod"]
	path = themes/PaperMod
	url = https://github.com/adityatelange/hugo-PaperMod.git
```

Expected: 配置正确

- [ ] **Step 3: 提交 submodule 配置**

```bash
git add .gitmodules themes/
git commit -m "feat: 添加 PaperMod 主题 (submodule)"
```

Expected: 变更已提交

---

## Chunk 3: 站点配置

### Task 3.1: 创建 hugo.yaml 主配置

**Files:**
- 创建: `hugo.yaml`

- [ ] **Step 1: 创建 Hugo 配置文件**

创建文件 `hugo.yaml`:

```yaml
baseURL: "https://wujunwei928.github.io/"
languageCode: "zh-cn"
title: "吴军伟的博客"
theme: "PaperMod"

enableRobotsTXT: true
buildDrafts: false
buildFuture: false
buildExpired: false

pagination:
  pageSize: 10

minify:
  disableXML: true
  minifyOutput: true

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
    - name: 搜索
      url: /search/
      weight: 5

# 站点参数
params:
  env: production
  title: "吴军伟的博客"
  description: "技术博客 - 分享编程经验与项目作品"
  keywords: [Blog, Portfolio, 技术, 编程]
  author: "吴军伟"
  images: ["<link or path of image for opengraph, twitter-cards>"]
  DateFormat: "2006-01-02"
  defaultTheme: auto
  disableThemeToggle: false

  # 首页信息
  homeInfoParams:
    Title: "你好，我是吴军伟 👋"
    Content: >
      欢迎来到我的技术博客！这里记录着我的编程学习笔记和项目作品。

  # 社交链接（可选）
  socialIcons:
    - name: github
      url: "https://github.com/wujunwei928"

  # 搜索功能
  fuseOpts:
    isCaseSensitive: false
    shouldSort: true
    location: 0
    distance: 1000
    patternExtent: 32
    minMatchCharLength: 0
    limit: 10
    keys: ["title", "permalink", "summary", "content"]

  # 文章配置
  showReadingTime: true
  showShareButtons: true
  showPostNavLinks: true
  showBreadCrumbs: true
  showCodeCopyButtons: true
  showWordCount: true
  showRssButtonInSectionTermList: true
  useHugoToc: true
  tocopen: true

  # 评论系统 (giscus) - 需要先配置 GitHub Discussions
  # 取消注释并填入你的 giscus 配置
  # giscus:
  #   repo: "wujunwei928/wujunwei928.github.io"
  #   repoId: ""  # 从 https://giscus.app/zh-CN 获取
  #   category: "Announcements"
  #   categoryId: ""  # 从 https://giscus.app/zh-CN 获取
  #   mapping: "pathname"
  #   reactionsEnabled: "1"
  #   emitMetadata: "0"

# 输出格式
outputs:
  home:
    - HTML
    - RSS
    - JSON

# RSS 配置
rss:
  limit: 20

# 代码高亮
markup:
  highlight:
    anchorLineNos: false
    codeFences: true
    guessSyntax: true
    lineNos: false
    noClasses: true
    style: "monokai"
  goldmark:
    renderer:
      unsafe: true

# 隐私配置（GDPR 合规）
privacy:
  googleAnalytics:
    disabled: true
```

- [ ] **Step 2: 验证配置语法**

```bash
hugo config
```

Expected: 配置加载成功，无错误

- [ ] **Step 3: 提交配置文件**

```bash
git add hugo.yaml
git commit -m "feat: 添加 Hugo 站点配置"
```

Expected: 变更已提交

---

### Task 3.2: 创建搜索页面

**Files:**
- 创建: `content/search.md`

- [ ] **Step 1: 创建搜索页面**

创建文件 `content/search.md`:

```markdown
---
title: "搜索"
layout: "search"
---
```

- [ ] **Step 2: 提交搜索页面**

```bash
git add content/search.md
git commit -m "feat: 添加搜索页面"
```

Expected: 变更已提交

---

## Chunk 4: 内容创建

### Task 4.1: 创建文章列表页

**Files:**
- 创建: `content/posts/_index.md`

- [ ] **Step 1: 创建文章列表页配置**

创建文件 `content/posts/_index.md`:

```markdown
---
title: "文章"
description: "技术文章和学习笔记"
type: "posts"
---
```

- [ ] **Step 2: 提交文章列表页**

```bash
git add content/posts/_index.md
git commit -m "feat: 添加文章列表页配置"
```

Expected: 变更已提交

---

### Task 4.2: 创建项目列表页

**Files:**
- 创建: `content/projects/_index.md`

- [ ] **Step 1: 创建项目列表页配置**

创建文件 `content/projects/_index.md`:

```markdown
---
title: "项目"
description: "我的开源项目和作品展示"
type: "projects"
---
```

- [ ] **Step 2: 提交项目列表页**

```bash
git add content/projects/_index.md
git commit -m "feat: 添加项目列表页配置"
```

Expected: 变更已提交

---

### Task 4.3: 创建关于页面

**Files:**
- 创建: `content/about.md`

- [ ] **Step 1: 创建关于页面**

创建文件 `content/about.md`:

```markdown
---
title: "关于"
description: "关于我"
---

## 关于我

你好！我是吴军伟，一名热爱技术的开发者。

## 技术栈

- 编程语言：Python, JavaScript, Go
- 前端框架：React, Vue
- 后端框架：Django, FastAPI, Gin
- 数据库：PostgreSQL, MySQL, Redis

## 联系方式

- GitHub: [@wujunwei928](https://github.com/wujunwei928)

---

欢迎通过以上方式与我交流！
```

- [ ] **Step 2: 提交关于页面**

```bash
git add content/about.md
git commit -m "feat: 添加关于页面"
```

Expected: 变更已提交

---

### Task 4.4: 创建示例文章

**Files:**
- 创建: `content/posts/welcome.md`

- [ ] **Step 1: 创建示例文章**

创建文件 `content/posts/welcome.md`:

```markdown
---
title: "欢迎来到我的博客"
date: 2026-03-16
description: "博客重建完成，新的开始"
tags: ["博客", "Hugo"]
categories: ["随笔"]
draft: false
---

## 新的开始

欢迎来到我的新博客！这是一个基于 Hugo + PaperMod 主题构建的技术博客。

## 博客功能

本博客支持以下功能：

- 🌙 深色/浅色模式切换
- 🔍 全站搜索
- 📑 文章目录
- 💻 代码高亮与复制
- 📡 RSS 订阅

## 代码示例

```python
def hello_world():
    print("Hello, Hugo!")

if __name__ == "__main__":
    hello_world()
```

## 后续计划

1. 持续更新技术文章
2. 添加项目作品展示
3. 完善评论系统

敬请期待！
```

- [ ] **Step 2: 提交示例文章**

```bash
git add content/posts/welcome.md
git commit -m "feat: 添加示例文章"
```

Expected: 变更已提交

---

### Task 4.5: 创建示例项目

**Files:**
- 创建: `content/projects/example-project.md`

- [ ] **Step 1: 创建示例项目**

创建文件 `content/projects/example-project.md`:

```markdown
---
title: "个人博客"
date: 2026-03-16
description: "基于 Hugo + PaperMod 的个人技术博客"
tags: ["Hugo", "博客", "静态站点"]
categories: ["项目"]
draft: false
external_link: ""
---

## 项目简介

这是我的个人技术博客，用于分享编程经验和学习笔记。

## 技术栈

| 组件 | 技术 |
|------|------|
| 静态生成器 | Hugo Extended |
| 主题 | PaperMod |
| 部署 | GitHub Pages |
| CI/CD | GitHub Actions |

## 功能特性

- 响应式设计，支持移动端
- 深色/浅色模式自动切换
- 全站搜索功能
- 代码语法高亮
- RSS 订阅支持

## 源码

[GitHub 仓库](https://github.com/wujunwei928/wujunwei928.github.io)
```

- [ ] **Step 2: 提交示例项目**

```bash
git add content/projects/example-project.md
git commit -m "feat: 添加示例项目"
```

Expected: 变更已提交

---

## Chunk 5: GitHub Actions 部署

### Task 5.1: 创建 GitHub Actions 工作流

**Files:**
- 创建: `.github/workflows/hugo.yml`

- [ ] **Step 1: 创建 workflows 目录**

```bash
mkdir -p .github/workflows
```

Expected: 目录创建成功

- [ ] **Step 2: 创建 Hugo 部署工作流**

创建文件 `.github/workflows/hugo.yml`:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches:
      - master
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.145.0
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: ${{ env.HUGO_VERSION }}
          extended: true

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4

      - name: Build with Hugo
        env:
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 3: 提交工作流配置**

```bash
git add .github/
git commit -m "feat: 添加 GitHub Actions 部署工作流"
```

Expected: 变更已提交

---

### Task 5.2: 处理 CNAME 文件

**Files:**
- 移动: `CNAME` -> `static/CNAME`

- [ ] **Step 1: 移动 CNAME 到 static 目录**

```bash
mv CNAME static/CNAME
```

Expected: CNAME 文件已移动

- [ ] **Step 2: 验证 CNAME 内容**

```bash
cat static/CNAME
```

Expected: 显示自定义域名

- [ ] **Step 3: 提交 CNAME 变更**

```bash
git add static/CNAME
git rm CNAME 2>/dev/null || true
git commit -m "fix: 移动 CNAME 到 static 目录"
```

Expected: 变更已提交

---

### Task 5.3: 创建项目 README

**Files:**
- 创建: `README.md`

- [ ] **Step 1: 创建 README 文件**

创建文件 `README.md`:

```markdown
# 吴军伟的博客

基于 Hugo + PaperMod 主题构建的个人技术博客。

## 本地开发

### 前置要求

- [Hugo Extended](https://gohugo.io/installation/) >= 0.120.0
- Git

### 克隆项目

```bash
git clone --recursive https://github.com/wujunwei928/wujunwei928.github.io.git
cd wujunwei928.github.io
```

### 本地预览

```bash
hugo server -D
```

访问 http://localhost:1313 查看站点。

## 项目结构

```
├── .github/workflows/    # GitHub Actions 配置
├── archetypes/           # 文章模板
├── content/              # 站点内容
│   ├── posts/           # 博客文章
│   ├── projects/        # 项目展示
│   └── about.md         # 关于页面
├── layouts/              # 自定义布局
├── static/               # 静态资源
├── themes/PaperMod/      # 主题 (submodule)
└── hugo.yaml             # Hugo 配置
```

## 新建文章

```bash
hugo new posts/my-new-post.md
```

## 部署

推送到 master 分支后，GitHub Actions 会自动构建并部署到 GitHub Pages。

## 技术栈

- [Hugo](https://gohugo.io/) - 静态站点生成器
- [PaperMod](https://github.com/adityatelange/hugo-PaperMod) - 主题
- [GitHub Actions](https://github.com/features/actions) - CI/CD
- [GitHub Pages](https://pages.github.com/) - 托管

## License

MIT
```

- [ ] **Step 2: 提交 README**

```bash
git add README.md
git commit -m "docs: 添加项目 README"
```

Expected: 变更已提交

---

## Chunk 6: 验证与部署

### Task 6.1: 本地验证

**Files:**
- N/A

- [ ] **Step 1: 启动本地服务器**

```bash
hugo server -D
```

Expected: 服务器启动成功，显示 `Web Server is available at http://localhost:1313/`

- [ ] **Step 2: 验证站点功能**

在浏览器中检查：
- [ ] 首页正常显示
- [ ] 导航菜单可用
- [ ] 深色/浅色模式切换
- [ ] 搜索功能正常
- [ ] 文章页面显示正常
- [ ] 项目页面显示正常
- [ ] 关于页面显示正常

- [ ] **Step 3: 停止本地服务器**

按 `Ctrl+C` 停止服务器

---

### Task 6.2: 推送并部署

**Files:**
- N/A

- [ ] **Step 1: 查看待推送的提交**

```bash
git log --oneline origin/master..HEAD
```

Expected: 显示所有新提交

- [ ] **Step 2: 推送到远程仓库**

```bash
git push origin master
```

Expected: 推送成功

- [ ] **Step 3: 检查 GitHub Actions 状态**

访问 https://github.com/wujunwei928/wujunwei928.github.io/actions

Expected: 工作流正在运行或已完成

---

### Task 6.3: 验证线上站点

**Files:**
- N/A

- [ ] **Step 1: 等待部署完成**

GitHub Actions 完成后（约 1-2 分钟）

- [ ] **Step 2: 访问线上站点**

访问 https://wujunwei928.github.io

Expected: 站点正常显示

- [ ] **Step 3: 验证线上功能**

- [ ] 深色模式切换
- [ ] 搜索功能
- [ ] 文章目录
- [ ] RSS 链接 (`/index.xml`)
- [ ] 移动端响应式

---

## 验收清单

- [ ] 本地 `hugo server` 可正常预览站点
- [ ] 所有功能正常工作（深色模式、搜索、TOC、代码高亮）
- [ ] GitHub Actions 构建成功
- [ ] 站点可通过 GitHub Pages 访问
- [ ] RSS 订阅链接可用 (`/index.xml`)

---

## 后续优化（可选）

### 启用 giscus 评论系统

1. 在 GitHub 仓库开启 Discussions 功能
2. 访问 https://giscus.app/zh-CN 获取配置
3. 取消 `hugo.yaml` 中 giscus 配置的注释并填入值
4. 重新部署

### 添加 Google Analytics

1. 创建 Google Analytics 账户获取 Measurement ID
2. 在 `hugo.yaml` 中添加配置：

```yaml
googleAnalytics: G-XXXXXXXXXX

privacy:
  googleAnalytics:
    disabled: false
```
