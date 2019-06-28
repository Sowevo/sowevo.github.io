---

title: '树莓派开启VNC,并配置端口转发'
tags:
  - linux
  - 树莓派
url: 37.html
id: 37
categories:
  - 折腾
date: 2017-09-15 16:15:18
---

要开启VNC首先要在树莓派上面安装VNC服务,最新版的Raspbian系统已经自带了VNC服务,只需要开启一下就行

开启服务
====

ssh登陆之后,输入
```shell
sudo raspi-config
```
进入raspi-config设置工具 选择Interfacing Options –>VNC 启动vnc服务,开启远程桌面服务 ![](https://up.sowevo.com/history/59ba27934b756.png) ![](https://up.sowevo.com/history/59ba2db359497.png) 最后选择Finish退出,并重启系统..

配置服务,开机启动
=========

首先 sudo vim /etc/init.d/vncserver 编辑文件,输入下面内容并保存,具体参数可以根据自己情况稍作修改
```shell
#!/bin/sh
### BEGIN INIT INFO
# Provides:          vncserver
# Required-Start:    $local_fs
# Required-Stop:     $local_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start/stop vncserver
### END INIT INFO

# More details see:
# http://www.penguintutor.com/linux/vnc

### Customize this entry
# Set the USER variable to the name of the user to start vncserver under
# 这里可以修改连接的用户,如果用其他用户,相应的修改
export USER='pi'
### End customization required

eval cd ~$USER

case "$1" in
  start)
    # 启动命令行。此处自定义分辨率、控制台号码或其它参数。
    su $USER -c '/usr/bin/vncserver -depth 16 -geometry 1920x1080 :1'
    echo "Starting VNC server for $USER "
    ;;
  stop)
    # 终止命令行。此处控制台号码与启动一致。
    su $USER -c '/usr/bin/vncserver -kill :1'
    echo "vncserver stopped"
    ;;
  *)
    echo "Usage: /etc/init.d/vncserver {start|stop}"
    exit 1
    ;;
esac
exit
```
并保存 赋予权限,并添加开机启动项
```shell
sudo chmod 755 /etc/init.d/vncserver
sudo update-rc.d vncserver defaults
```
最后重启树莓派~ 正常情况下重新启动之后就可以连接VNC了 打开你的VNC客户端,这里使用的Chrom拓展程序[VNC® Viewer for Google Chrome™](https://chrome.google.com/webstore/detail/vnc%C2%AE-viewer-for-google-ch/iabmpiboiopbgfabjmgeedhcmjenhbla?utm_source=chrome-app-launcher-info-dialog),不用额外安装,不错 输入你的ip注意不要忘记 **:1** 对应自己配置文件里面的控制台号码 ![vnc](https://up.sowevo.com/history/59bb84dfe9196.png) 首次连接还会询问是否信任此设备,信任就行 输入用户信息 

**用户是linux系统的用户,需要与配置文件中指定的一致,密码为用户对应的linux密码** ![VNC](https://up.sowevo.com/history/59bb84dfdd9e7.png) OK,连接成功~ ![vnc](https://up.sowevo.com/history/59bb84dfecd3d.png) 家里有公网ip的小伙伴,还可以为VNC服务设置端口转发,没有的请略过

VNC的端口转发
========

首先要知道VNC需要哪一个端口号 谷歌一下知道 VNC所需的端口不固定,一个控制台号需要对应三个端口 RFB(RemoteFrameBuffer)协议     默认端口 : 5900+控制台号 HTTP协议                                           默认端口 : 5800+控制台号 X协议                                                   默认端口 : 6000+控制台号 配置文件里指定的控制台号为 :1 所需的三个端口为5801,5901,6001三个,把这三个端口加入路由器的端口转发规则里 ![](https://up.sowevo.com/history/59bb8be123693.png) 保存,输入你的公网ip测试一下,连接成功~!!