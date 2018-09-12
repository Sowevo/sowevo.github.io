---
title: 群晖搭建h5ai
url: 51.html
id: 51
categories:
  - 网站
date: 2018-02-03 04:30:31
tags:
---

# 群晖搭建h5ai

群晖NAS自带的文件共享工具浏览器打开实在太费劲了,自己想办法给他加一个简单点的文件目录列表程序
选择使用[h5ai](https://larsjung.de/h5ai/)这个程序,这里是官方的[演示站点](https://larsjung.de/h5ai/demo/)
网上找到的[部署教程](https://www.htcp.net/3643.html)都是针对普通linux服务器的,所有的虚拟主机之类的配置都是可以自己配置的
群晖这个稍微麻烦一点 其中的针对nginx的配置文件的处理有些不同
群晖自己有自带的WebStation套件,使用WebStation可以对nginx进行简单的配置 使用WebStation新建虚拟主机
![QQ截图20180203033825.png](https://i.loli.net/2018/02/03/5a74be42d2281.png "QQ截图20180203033825.png") 
其中文档根目录选择的是你需要在h5ai中列出的文件的内容,端口号为后续打开h5ai使用 配置完成后,WebStation就为你自动创建了nginx的配置文件 但是根据官方的说明文档,需要在配置文件中增加

```nginx
index  index.html  index.php  /_h5ai/public/index.php;
```

问题是WebStation的配置文件在哪里呢? 百度一下有了结果~ 如果要改nginx的配置文件,需要使用ssh客户端连接上群辉服务器,使用admin用户登录 执行下列命令查看nginx的配置文件
```shell
cat /etc/nginx/app.d/server.webstation-vhost.conf
```
结果如下图
![QQ截图20180203035008.png](https://i.loli.net/2018/02/03/5a74c10beb4a0.png "QQ截图20180203035008.png") 
如果之前有增加过其他的虚拟主机,可能会有多个配置信息 其中红线部分表示在这个位置下的所有以user.conf开头的配置文件,都会作为配置文件的一部分被引入
```shell
cd /usr/local/etc/nginx/conf.d/e5e2068d-bde3-4b31-a963-0b64087fcdcf/
sudo vim user.conf.h5ai
```
所以我们只需要在这个路径下新建一个配置文件,将文档中提到的内容写入进去
index  index.html  index.php  /_h5ai/public/index.php;
重启nginx服务
```shell
sudo nginx -s reload
```
环境配置完成,部署h5ai 将h5ai解压到刚才新建虚拟主机选择的目录下 ![QQ截图20180203040324.png](https://i.loli.net/2018/02/03/5a74c44aeaf9c.png "QQ截图20180203040324.png") 并对其中两个目录授予666的权限
```shell
sudo chmod 666 _h5ai/private/cache
sudo chmod 666 _h5ai/public/cache
```
使用ip加10005端口即可访问 ![QQ截图20180203043006.png](https://i.loli.net/2018/02/03/5a74ca5ad2797.png "QQ截图20180203043006.png")