---
title: 解决VIM中文乱码
tags:
  - linux
url: 12.html
id: 12
categories:
  - 代码
date: 2017-09-08 23:33:54
updated: 2017-09-08 23:33:54
---

刚才改WordPress的配置文件,发现里面的中文注释不能好好显示,于是百度之~~~ 解决方案 vim ~/.vimrc 在用户文件夹下创建.vimrc文件并写入

```sh
set fileencodings=utf-8,gb2312,gbk,gb18030
set termencoding=utf-8
set fileformats=unix
set encoding=prc
```



另外,如果出现**^M**这样的字符上面第三行改成

set fileformats=unix,dos

搞定~~