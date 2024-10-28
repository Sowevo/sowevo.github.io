---
title: 代理无法提交git
date: 2024-10-29 02:04:25
updated: 2024-10-29 02:04:25
tags:
  - linux
  - git
---
# 通过 HTTPS 端口使用 SSH

在某些网络环境中（奇怪的代理工具），22 端口可能被屏蔽，导致无法使用 SSH 进行 Git 推送。

这时，我们可以利用 SSH over HTTPS 的方法，通过 443 端口实现推送。以下是详细步骤：

1. 编辑 SSH 配置文件
   首先，需要编辑你的 SSH 配置文件 `~/.ssh/config`。
   将以下配置加到该文件中：

   ```bash
   Host github.com
   Hostname ssh.github.com
        Port 443
        User git
   ```

   也可以使用以下命令一键添加

   ```bash
   echo -e "Host github.com\n\tHostName ssh.github.com\n\tPort 443\n\tUser git" >> ~/.ssh/config
   ```

   这条命令将指定 GitHub 的主机名为 ssh.github.com， 并将端口设置为 443，用户为 git。

2. 确保配置生效
   配置完成后，可以通过以下命令验证设置是否生效：

   ```bash
   $ ssh -T git@github.com
   # Hi USERNAME! You've successfully authenticated, but GitHub does not
   # provide shell access.
   ```

3. 使用 Git 推送
   现在，你可以正常使用 Git 命令进行推送。例如：

   ```bash
   git add .
   git commit -m "Your commit message"
   git push origin main
   ```

   由于你已经将 SSH 流量封装在 HTTPS 中，推送操作应该能够顺利完成。