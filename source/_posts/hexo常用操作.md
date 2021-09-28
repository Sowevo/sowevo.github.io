---
title: hexo常用操作
date: 2020-03-26 01:05:41
updated: 2021-03-18 22:39:24
tags: Hexo
---

好久没有更新文章了,我甚至都不记得的我还有个网站了
折腾了半天终于能更新了
记录下相关命令
防止以后还得到处找

# 本地恢复hexo的环境

- 安装nodejs

  ```shell
  # mac
  $ brew install node
  # centos
  $ yum install -y nodejs
  # Ubuntu 
  $ apt install nodejs npm -y
  ```

- node的国内镜像源配置

  ```shell
  $ npm config set registry https://registry.npm.taobao.org
  ```

- 从git克隆项目文件

  ```shell
  # 因为使用了子项目管理主题,加上'--recursive'参数 
  $ git clone --recursive git@github.com:Sowevo/sowevo.github.io.git
  ```
- 更新主题子项目
  ```shell
  # 进入项目
  $ cd ~/sowevo.github.io
  # 更新子项目的代码
  $ git submodule update --remote --merge
  # 如果有更新,提交一下
  $ git add themes/next
  $ git commit -m "themes submodule updated"
  $ git push
  ```

- 进入项目

  ```shell
  $ cd ~/sowevo.github.io
  ```

- 安装相关依赖

  ```shell
  # 安装hexo
  $ npm install -g hexo-cli
  # 安装依赖
  $ npm install
  # 安装hexo git 部署插件
  $ npm install hexo-deployer-git
  # 其他插件
  $ npm install hexo-generator-sitemap
  $ npm install hexo-generator-baidu-sitemap
  $ npm install hexo-generator-feed
  ```

- 至此,环境恢复完成,可以开始干活了!!!

- 相关命令
  ```shell
  # 新建文章
  $ hexo n "我的博客"
  # 生成静态文件
  $ hexo g
  # 运行服务
  $ hexo s
  # 部署
  $ hexo d
  ```

- **记得提交hexo分支的源文件**