---
title: Mackup 使用指南
date: 2025-11-27 00:22:01
updated: 2025-11-27 00:55:00
tags:
  - Mackup
  - macOS
  - Dotfiles
---
# Mackup 的使用方式

Mackup 是一个用于备份和同步应用配置（dotfiles）的工具，支持 iCloud、Dropbox 等云存储。  
自 macOS Sonoma 起，Mackup 的旧的 link 教程很多都不再适用，因此非常有必要重新梳理一套“安全且可用”的最佳实践。

本文基于最新 macOS 行为，整理了一套 **仅同步 CLI 工具（如 Codex）的最佳方案**，同时避免同步 GUI 应用偏好造成的问题。

## Mackup 是什么？
Mackup 可以将应用配置文件同步到云盘，在多台电脑之间保持统一环境。  
尤其适合 CLI 工具，例如：
- git
- zsh
- ssh
- Codex（本文示例）
<!-- more -->
## copy 模式 vs link 模式

| 模式          | 行为                 | 实时同步 | 安全性         |
|-------------|--------------------|----|-------------|
| **copy 模式** | 复制文件到云盘，再从云盘恢复     | 否  | 安全          |
| **link 模式** | 文件替换为 symlink 指向云盘 | 是  | 对GUI应用可能有风险 |

### GUI应用可能有风险

macOS Sonoma 起，系统偏好设置（`~/Library/Preferences/*.plist`）必须写入本地真实文件，不能放 symlink 后面。  
一旦 GUI 应用的 plist 用了 symlink：

*   偏好保存失败

*   自动恢复默认

*   偏好文件损坏

*   应用可能无法启动

### 终端应用没问题

git,zsh,ssh等终端应用没有问题

这些应用使用的都是普通 JSON / TOML 文件：

```
~/.codex/auth.json
~/.codex/config.toml
```
不属于 plist，不受系统限制，可放心使用 link 模式。

## 配置 Mackup 的例子

下面先给出最小化、安全的 Codex 配置

### mackup配置

编辑：`~/.mackup.cfg`

```ini
[storage]
# 使用的存储引擎
engine = icloud
# 存储目录
directory = Mackup

[applications_to_sync]
# 你要同步的应用
codex

[applications_to_ignore]
# 可选：排除你不想同步的应用
# ssh
# git
# vscode
```

这样能确保：

*   Mackup 只管 Codex
*   不会误操作任何 GUI 应用
*   不会触发 Sonoma 的偏好限制

### 自定义同步文件配置

如果你想让 Mackup 管理更多 Codex 文件（如脚本、文档），可在`~/.mackup`下面新建文件`codex.cfg`，内容如下：
```ini
[application]
name = Codex

[configuration_files]
.codex/auth.json
.codex/config.toml
.codex/notify_on_finish.sh
.codex/AGENTS.md
```
⚠️ 非常重要：
> `~/.mackup/codex.cfg` 会**覆盖**内置规则，不是追加。  
> 所有要同步的文件必须自己写全。

这样你就可以自由扩展 Codex 的同步文件范围。



## 使用流程

### 主力机：首次安装 link 模式

1. 安装 Mackup

```shell
brew install mackup
```
2. 编写配置文件：就上面例子中的配置文件

3. 在主力机上初始化：
```shell
mackup link install
```

它会进行如下操作

> - 将 Codex 配置复制到云盘
> - 删除本地原始文件
> - 生成 symlink 指向云端文件

4. 最后检查：
```shell
ls -l ~/.codex
```

应看到：

```shell
auth.json -> ~/Library/.../Mackup/.codex/auth.json
config.toml -> ~/Library/.../Mackup/.codex/config.toml
```


这说明 link 模式已成功启用。

### 新电脑：如何恢复 

1. 安装 Mackup
```shell
brew install mackup
```

2. 等待 iCloud/Dropbox 同步完整的 Codex 备份

3. 编写配置文件：还是上面例子中的配置文件
   
   > ⚠️Mackup不会同步自己的配置文件
   > 需要自己处理配置文件的同步

4. 执行恢复

  ```shell
  mackup link
  ```
5. 检查

  ```shell
  ls -l ~/.codex
  ```

应看到：

```shell
auth.json -> ~/Library/.../Mackup/.codex/auth.json
config.toml -> ~/Library/.../Mackup/.codex/config.toml
```