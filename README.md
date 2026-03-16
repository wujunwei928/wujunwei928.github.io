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
