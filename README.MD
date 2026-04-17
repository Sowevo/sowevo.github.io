# sowevo.github.io

个人 Hexo 博客仓库：<https://sowevo.github.io/>

## 首次使用（新机器）

### 1) 准备环境

- 安装 Node.js（建议 LTS 版本）
- 安装 Git

检查版本：

```bash
node -v
npm -v
git --version
```

### 2) 克隆仓库（含主题子模块）

二选一：

```bash
git clone --recursive git@github.com:Sowevo/sowevo.github.io.git
```

```bash
git clone --recursive https://github.com/Sowevo/sowevo.github.io.git
```

如果已经克隆过但忘记 `--recursive`，在仓库内执行：

```bash
git submodule sync --recursive
git submodule update --init --recursive
```

### 3) 安装依赖

```bash
cd sowevo.github.io
npm install
```

## 本地启动

```bash
npx hexo clean
npx hexo g
npx hexo s -o
```

默认访问：<http://localhost:4000>

## 常用命令

新建文章：

```bash
npx hexo new "文章标题"
```

重新生成：

```bash
npx hexo g
```

本地预览：

```bash
npx hexo s
```

## 发布部署（GitHub Actions）

当前发布由 GitHub Actions 自动完成（`.github/workflows/deploy.yml`）。

触发方式：

- 推送到 `hexo` 分支时自动触发
- 在 GitHub Actions 页面手动触发（`workflow_dispatch`）

日常发布建议：

```bash
git add .
git commit -m "your message"
git push origin hexo
```

本地依然可以手动部署（兜底）：

```bash
npx hexo clean && npx hexo g
npx hexo d
```

## 备注

- 主题位于 `themes/next`（Git 子模块）。
- 如果主题有更新，先拉取子模块，再提交子模块指针变更。
