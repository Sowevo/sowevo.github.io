---
title: Openwrt开启外网访问并配置SSL
date: 2021-03-18 22:43:11
updated: 2021-03-18 22:43:11
tags: openwrt
---
记录一下OpenWrt开启外网访问并绑定SSL证书的操作
### 外网访问
- 关闭只允许内网访问的设置(去掉勾选)
![](https://up.sowevo.com/img/20210318224657.png)
- 防火墙==>端口转发 中添加端口转发,将外网5500应设置OpenWrt的80端口
![](https://up.sowevo.com/img/20210318225037.png)
- 通过域名+5500端口即可访问
### 添加SSL证书
- 编辑uHTTPd的配置文件,打开路由器的SSL 443端口
  
  ```shell
  $ vi /etc/config/uhttpd
  ```
  在原有配置 list listen_http '0.0.0.0:80'下面加入以下两行
  ```shell
    # 注意是https
    list listen_https '0.0.0.0:443'
    list listen_https '[::]:443'
  ```
  
- 输入以下命令安装 acme.sh
    ```shell
    $ curl  https://get.acme.sh | sh
    ```

    可以看到此时已经添加了定时任务

    ![](https://up.sowevo.com/img/20210318225615.png)


- 此处使用 cloudflare  接口进行自动认证
  
  - 获取CF_Email,CF_Key两个参数,并执行以下命令
    ```shell
    export CF_Email="abcde@126.com"
    export CF_Key="de6132435csofe45464650w4646464602"
    ```

- 输入以下命令,运行acme.sh生成证书,并将生成的证书放到uHTTPd的默认存储位置,最后自动重启uHTTPd
  ```shell
  $ /root/.acme.sh/acme.sh --issue --dns dns_cf -d sowevo.com -d *.sowevo.com --keypath /etc/uhttpd.key --fullchainpath /etc/uhttpd.crt --reloadcmd "/etc/init.d/uhttpd restart"
  ```
  稍等一会命令执行完成(不同的dns厂商时间会有不同),证书就申请完毕

- 将路由器的443端口映射到外网
![](https://up.sowevo.com/img/20210318231508.png)
家宽的443端口竟然没有封,就先使着吧...
