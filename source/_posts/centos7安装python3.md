---
title: centos7安装python3
date: 2018-09-16 10:48:01
updated: 2018-09-16 10:48:01
tags: linux
---

centos现在默认的python还是2.x版本的,没事总要给他换个3.x版本

# 安装安装python3可能使用相关软件

```bash
yum groupinstall 'Development Tools'
yum install -y ncurses-libs zlib-devel mysql-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel gcc
```

# 安装安装python3

- [python官网](https://www.python.org)下载安装包

- 下载最新版源码，使用make altinstall，如果使用make install，在系统中将会有两个不同版本的Python在/usr/bin/目录中。这将会导致很多问题，而且不好处理。

  ```shell
  # 此处以3.6.5为例
  wget https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tgz
  tar -xzvf Python-3.6.5.tgz -C  /tmp
  cd  /tmp/Python-3.6.5/
  ./configure --prefix=/usr/local
  make
  make altinstall
  ```

- 更改/usr/bin/python链接,如果有pip也给更改了

  ```shell
  cd /usr/bin
  mv  python python.backup
  ln -s /usr/local/bin/python3.6 /usr/bin/python
  ln -s /usr/local/bin/python3.6 /usr/bin/python3
  mv  pip pip.backup
  ln -s /usr/local/bin/pip3.6 /usr/bin/pip
  ln -s /usr/local/bin/pip3.6 /usr/bin/pip3
  ```

- 更改yum脚本的python依赖

  ```shell
  cd /usr/bin
  ls yum*
  ```

- 修改上面命令列出的文件更改以上文件头

  ```
  #!/usr/bin/python 改为 #!/usr/bin/python2
  ```

- 修改gnome-tweak-tool配置文件(如果有的话)

  ```
  vim /usr/bin/gnome-tweak-tool
  #!/usr/bin/python 改为 #!/usr/bin/python2
  ```

- 修改urlgrabber配置文件(如果有的话)

  ```
  vim /usr/libexec/urlgrabber-ext-down
  #!/usr/bin/python 改为 #!/usr/bin/python2
  ```