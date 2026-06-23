# 题库博客站点设计

## 概述

将现有 Markdown 题库转换为基于 Hugo + Hugo Book 主题的静态博客站点，部署到 GitHub Pages。

## 技术选型

- **静态站点生成器**：Hugo
- **主题**：Hugo Book（文档风格，侧边栏导航）
- **部署**：GitHub Actions → GitHub Pages
- **访问地址**：`https://tooplick.github.io/tiku/`

## 项目结构

在仓库根目录初始化 Hugo 站点，现有题库文件迁移到 `content/docs/` 下。

```
tiku/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions 自动部署
├── content/
│   ├── _index.md               # 首页
│   └── docs/
│       ├── _index.md           # 文档根目录
│       ├── axure/
│       │   ├── _index.md       # Axure 科目首页
│       │   ├── 选择题.md
│       │   ├── 判断题.md
│       │   ├── 填空题.md
│       │   └── 简答题.md
│       ├── python/
│       │   ├── _index.md
│       │   ├── 选择题.md
│       │   ├── 判断题.md
│       │   ├── 填空题.md
│       │   ├── 简答题.md
│       │   └── 大题.md
│       ├── software-test/
│       │   ├── _index.md
│       │   ├── 选择题1.md
│       │   ├── 选择题2.md
│       │   ├── 判断题.md
│       │   ├── 填空题.md
│       │   ├── 简答题.md
│       │   └── 程序填空题.md
│       ├── vue/
│       │   ├── _index.md
│       │   ├── 判断题.md
│       │   ├── 单选题.md
│       │   └── 填空题.md
│       └── mao/
│           ├── _index.md
│           └── 毛概题库.md
├── themes/
│   └── hugo-book/              # Hugo Book 主题（git submodule）
├── static/                     # 静态资源
├── hugo.toml                   # Hugo 配置文件
├── LICENSE
└── README.md
```

## 内容迁移

### 文件映射

| 原路径 | 目标路径 |
|--------|----------|
| `Axure/选择.md` | `content/docs/axure/选择题.md` |
| `Axure/判断.md` | `content/docs/axure/判断题.md` |
| `Axure/填空.md` | `content/docs/axure/填空题.md` |
| `Axure/简答.md` | `content/docs/axure/简答题.md` |
| `Python/Python选择题.md` | `content/docs/python/选择题.md` |
| `Python/Python判断题.md` | `content/docs/python/判断题.md` |
| `Python/Python填空题.md` | `content/docs/python/填空题.md` |
| `Python/Python简答题.md` | `content/docs/python/简答题.md` |
| `Python/Python大题.md` | `content/docs/python/大题.md` |
| `Softwore-test/Softwore-test选择题1.md` | `content/docs/software-test/选择题1.md` |
| `Softwore-test/Softwore-test选择题2.md` | `content/docs/software-test/选择题2.md` |
| `Softwore-test/Softwore-test判断题.md` | `content/docs/software-test/判断题.md` |
| `Softwore-test/Softwore-test填空题.md` | `content/docs/software-test/填空题.md` |
| `Softwore-test/Softwore-test简答题.md` | `content/docs/software-test/简答题.md` |
| `Softwore-test/Softwore-test程序填空题.md` | `content/docs/software-test/程序填空题.md` |
| `Vue/Vue判断题.md` | `content/docs/vue/判断题.md` |
| `Vue/Vue单选题.md` | `content/docs/vue/单选题.md` |
| `Vue/Vue填空题.md` | `content/docs/vue/填空题.md` |
| `Other/毛概题库.md` | `content/docs/mao/毛概题库.md` |

### Front Matter

每个 Markdown 文件顶部添加 Hugo front matter：

```yaml
---
title: "Python 选择题"
weight: 1
---
```

- `title`：页面标题，显示在侧边栏和页面顶部
- `weight`：控制同一目录下页面的排序（数字越小越靠前）

### 内容保留

题目内容（标题、选项、答案、解析）原样保留，不改写。Hugo 直接渲染 Markdown。

## Hugo 配置

`hugo.toml` 核心配置：

```toml
baseURL = "https://tooplick.github.io/tiku/"
languageCode = "zh-cn"
title = "题库笔记"
theme = "hugo-book"

[params]
  BookTheme = "auto"           # 自动亮/暗色模式
  BookToC = true               # 显示目录
  BookSection = "docs"         # 文档根目录
  BookRepo = "https://github.com/tooplick/tiku"
```

## 侧边栏导航

Hugo Book 自动生成侧边栏，基于 `content/docs/` 的目录结构：

```
📚 题库笔记
├── Axure
│   ├── 选择题
│   ├── 判断题
│   ├── 填空题
│   └── 简答题
├── Python
│   ├── 选择题
│   ├── 判断题
│   ├── 填空题
│   ├── 简答题
│   └── 大题
├── 软件测试
│   ├── 选择题1
│   ├── 选择题2
│   ├── 判断题
│   ├── 填空题
│   ├── 简答题
│   └── 程序填空题
├── Vue
│   ├── 判断题
│   ├── 单选题
│   └── 填空题
└── 毛概
    └── 毛概题库
```

## 部署

### GitHub Actions 工作流

创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Hugo to GitHub Pages

on:
  push:
    branches:
      - main

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'

      - name: Build
        run: hugo --minify

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

### GitHub 仓库设置

需要在 GitHub 仓库设置中：
1. 进入 Settings → Pages
2. Source 选择 "GitHub Actions"

## 不做的事情

- 不添加交互式刷题功能（纯静态浏览）
- 不改写原始题目内容
- 不添加评论系统
- 不添加搜索功能（Hugo Book 默认无搜索，后续可选加）
- 原始题库目录（Axure/、Python/、Softwore-test/、Vue/、Other/、毛概题库.md）在迁移后删除，内容已复制到 content/docs/

## 实施步骤

1. 在仓库根目录初始化 Hugo 站点
2. 安装 Hugo Book 主题（git submodule）
3. 编写 hugo.toml 配置
4. 创建 content/docs/ 目录结构
5. 迁移所有 Markdown 文件（添加 front matter）
6. 创建首页 content/_index.md
7. 创建 GitHub Actions 部署工作流
8. 本地测试验证
9. 推送到 main 分支，GitHub Actions 自动部署
