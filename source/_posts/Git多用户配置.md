---
title: Git多用户配置
date: 2021-05-08 22:36:41
updated: 2021-05-08 22:36:41
tags:
  - linux
  - git
---

# Git多用户配置

## 问题

在工作与生活中,我们难免会遇到同时使用多个Git账号的情况

- 私人的项目在Github上托管,使用的user.name配置是Sowevo,使用的user.email是user@sowevo.com

- 公司的项目在Gitlab上托管,使用的user.name配置是lisi,使用的user.email是lisi@gitlab.com

这时就会遇到这样的问题,提交到Gitlab的代码署名确是<Sowevo>user@sowevo.com,经常用混

## 解决方案

### 按工程配置

git的配置可以放在三个地方：

- `/etc/gitconfig` 系统配置,对所有用户有效,一般不用

- `~/.gitconfig` 用户配置,仅对当前用户生有效

  ```shell
  # 全局配置
  $ git config --global user.name "Sowevo"
  $ git config --global user.email "user@sowevo.com"
  ```

- `projectPath/.git/config` 项目配置，仅对当前项目生效

  ```shell
  # 项目配置
  $ git config user.name "lisi"
  $ git config user.email "lisi@gitlab.com"
  ```

三种配置的优先级为 `项目配置>用户配置>系统配置`

此时可以利用项目配置优先的规则,对公司项目单独设置user.name与user.email,但是项目如果比较多的话,每一个都要设置,比较繁琐

可以使用下一种方式配置

### 按目录配置

Git的2.13.0之后的版本包含了一个新的功能,`includeIf`配置可以通过路径匹配不同的配置文件

以下操作以macOS为例

- 新建或编辑`~/.gitconfig`,确保其中存在以下内容

  ```ini
  [user]
      name = sowevo
      email = user@sowevo.com
  [includeIf "gitdir:~/git/gitlab/"]
      path = .gitconfig-work
  ```
  
- 新建`~/.gitconfig-work`文件

  ```ini
  [user]
      name = lisi
      email = lisi@gitlab.com
  ```

此时`~/git/gitlab/`路径下的git项目全部都会使用`.gitconfig-work`的配置

此路径下的配置优先级为`项目配置>.gitconfig-work配置>用户配置>系统配置`

其他路径下的配置优先级为`项目配置>用户配置>系统配置`

#### **注意**:公司项目只能克隆到`~/git/gitlab/`路径下