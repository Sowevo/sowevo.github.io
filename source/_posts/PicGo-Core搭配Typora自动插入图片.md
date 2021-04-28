---
title: PicGo-Core搭配Typora自动插入图片
date: 2021-04-23 15:35:49
updated: 2021-4-28 22:34:47
tags: 
  - linux
  - 图床
---

使用Typora写笔记的时候经常需要贴个图

Typora支持自动上传图片到图床,需要搭配一些第三方的图床工具

这里选择了PicGo-Core

# 设置PicGo-Core

- 安装PicGo-Core

  ```shell
  # 依赖node
  $ npm install picgo -g 
  # or 
  $ yarn global add picgo
  ```

- 配置PicGo-Core

  我使用的是腾讯云COS,不同的图床配置不一样,具体[参考文档](https://picgo.github.io/PicGo-Core-Doc/zh/guide/config.html#picbed-tcyun)

  ```shell
  # 设置图床
  $ picgo set uploader 
  # 选择默认图床
  $ picgo use uploader 
  ```

- 测试上传

  ```shell
  # 上传具体路径图片
  $ picgo upload /xxx/xxx.jpg
  
  # 上传剪贴板里的第一张图片（上传时会将格式转成png）
  $ picgo upload
  ```

# 设置Typora

## Mac设置步骤

- 获取node与picgo的绝对路径

  ```shell
  # 获取node的路径 我的是 /usr/local/bin/node
  $ which node
  # 获取picgo的路径 我的是 /usr/local/bin/picgo
  $ which picgo
  ```

- 设置Typora的上传

  - 打开**偏好设置**-->**图像**
  - **插入图片时**选择**上传图片**
  - **上传图片设定**选择**Custom Command**
  - 命令填入`[your node path] [your picgo-core path] upload`,使用上一步获取的路径替换,例如我的是`/usr/local/bin/node /usr/local/bin/picgo upload`

  ![image-20210426162439566](https://up.sowevo.com/img/image-20210426162439566.png)

## Winsdows设置步骤

- 设置Typora的上传

  - 打开**偏好设置**-->**图像**

  - **插入图片时**选择**上传图片**

  - **上传图片设定**选择**Custom Command**

  - 命令填入`picgo upload`

    ![image-20210428223155681](https://up.sowevo.com/img/image-20210428223155681.png)
