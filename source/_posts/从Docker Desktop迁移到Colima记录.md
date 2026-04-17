---
title: 从Docker Desktop迁移到Colima记录
date: 2026-04-17 12:31:00
updated: 2026-04-17 12:31:00
tags:
  - Docker
  - Colima
  - macOS
---

# 从Docker Desktop迁移到Colima记录

最近折腾了一把，把本机 Docker Desktop 卸掉，切到了 Colima。

主要原因很简单：公司合规策略不允许继续使用 Docker Desktop。

这篇就当个流水账，给未来的自己留个复盘。

<!--more-->

## 1. 背景

- 公司对 Docker Desktop 的使用有合规限制。
- 本机需要保留容器开发能力（至少能跑 Redis / MariaDB）。
- 目标是：
  - 不用 Docker Desktop
  - 用 Colima + Docker CLI
  - `docker compose` 可用

## 2. 卸载 Docker Desktop

先确认：

```bash
brew list | grep -i docker
ps -ef | grep -i docker | grep -v grep
```

如果还有残留进程，先停掉再卸载。Homebrew 卸载命令：

```bash
brew uninstall --cask docker-desktop
```

如果卡在系统权限（`com.docker.vmnetd` 之类），可以手动处理系统残留：

```bash
sudo pkill -f com.docker.vmnetd || true
```

最后再次确认：

```bash
brew list | grep -i docker || echo "已无 docker"
ps -ef | grep -i docker | grep -v grep || echo "已无 docker 进程"
```

## 3. 安装 Colima

安装并启动：

```bash
brew install colima docker
colima start --cpu 4 --memory 8 --disk 60
```

> 说明：资源参数只需要首次（或改配置时）设置，之后日常 `colima start` 即可。

确认 context：

```bash
docker context ls
docker context use colima
```

如果看到当前是 `colima *`，并且 endpoint 是 `~/.colima/default/docker.sock`，就对了。

## 4. `docker compose` 报 unknown command 的坑

现象：

```bash
docker compose version
# docker: unknown command: docker compose
```

原因：安装了 `docker-compose` 插件，但 Docker CLI 没扫描到 Homebrew 插件目录。

修复：在 `~/.docker/config.json` 增加：

```json
{
  "cliPluginsExtraDirs": [
    "/opt/homebrew/lib/docker/cli-plugins"
  ]
}
```

验证：

```bash
docker compose version
# Docker Compose version x.x.x
```

兼容旧命令（可选）：

```bash
# ~/.zshrc
alias docker-compose='docker compose'
```

## 5. 防止忘记先启动 Colima

在 `~/.zshrc` 里加一个轻量保护：

```bash
docker() {
  if ! colima status >/dev/null 2>&1; then
    echo "Colima 未启动，先执行: colima start"
    return 1
  fi
  command docker "$@"
}
```

这样你直接敲 `docker ...` 时，如果 Colima 没启动，会给明确提示，不会莫名其妙报 socket 错误。

## 6. Docker Hello 验证启动

迁移完成后，先用一个无状态容器做最小验证，确认 Docker CLI 与 Colima 已正常联通。

```bash
docker run --rm hello-world
```

如果输出里看到 `Hello from Docker!`，说明容器运行链路已经正常。

也可以再补一条查看运行时信息：

```bash
docker info
```

## 7. 小结

这次迁移的关键点其实就三个：

1. 不要混淆 Docker Desktop socket 和 Colima socket。
2. `docker compose` 不可用时，优先检查插件目录是否被 Docker CLI 扫描。
3. 给 shell 加一个“Colima 未启动提醒”，能省很多排查时间。

到这里，本机容器环境已经完全不依赖 Docker Desktop 了，日常开发够用。
