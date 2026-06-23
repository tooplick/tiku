# 题库笔记

基于 Hugo 构建的题库复习笔记站点，部署在 [GitHub Pages](https://tooplick.github.io/tiku/)。

## 科目

- **Axure** - Axure RP 10 相关题目
- **Python** - Python 编程基础题目
- **软件测试** - 软件测试理论与实践题目
- **Vue** - Vue.js 框架相关题目
- **毛概** - 毛泽东思想概论题目

## 本地开发

```bash
# 安装 Hugo (需要 extended 版本 >= 0.158.0)
# 启动开发服务器
hugo server -D

# 构建生产版本
hugo --minify
```

## 部署

站点通过 GitHub Actions 自动部署到 GitHub Pages。推送到 `main` 分支即触发部署。

## 目录结构

```
content/
├── _index.md          # 首页
└── docs/
    ├── _index.md      # 文档根目录
    ├── axure/         # Axure 题库
    ├── python/        # Python 题库
    ├── software-test/ # 软件测试题库
    ├── vue/           # Vue 题库
    └── mao/           # 毛概题库
```
