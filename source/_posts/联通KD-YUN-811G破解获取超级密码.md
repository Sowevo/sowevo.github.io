---
title: 联通KD-YUN-811G破解获取超级密码
date: 2019-07-24 21:25:13
tags:
  - 网络
categories:
  - 折腾
---
新装联通宽带一条,300M
送了一台KD-YUN-811G的光猫,也不知道是什么牌子的
想要自己改桥接模式,
百度搜索一番后,找到了方法
记录一下过程
# 此方法同样适用于 
- TEWA-800E

  
# 一步一步来~
- 进入光猫登录界面
  ![登录界面](https://up.sowevo.com/img/20190724214706.png)
- F12进入开发者工具,在Elements中搜索user_name找到这里
<!-- more -->
  ![](https://up.sowevo.com/img/20190724214835.png)
- 将元素id改为CUAdmin,此时界面上出现用户名输入框,如图
  ![](https://up.sowevo.com/img/20190724215127.png)
- 切换到Sources标签页,找到submitFrm()函数
  ![](https://up.sowevo.com/img/20190724215618.png)
- 将函数复制出来进行修改,删掉其中的
  ```javascript
  if(username.value!='user'){
      alert('请输入正确的用户账户!');
      return;
  }
  ```
  将其改为
  ```Javascript
  function submitFrm()
  {
      with(document.forms[0])
      {
          if ( password.value.length == 0 ) {
              alert('密码不得为空!');
              return;
          }
      }
      document.getElementById('loginfrm').submit();
  }
  ```
  将修改后的函数输入到Console标签页,回车后执行
- 两个输入框都输入CUAdmin后点击GO登录
  ![](https://up.sowevo.com/img/20190724220620.png)
- 成功进入后台
  ![](https://up.sowevo.com/img/20190724220828.png)

# 简单处理
- 其实还有简单点的方法...
  打开登陆页,打开开发者工具,切换到Console标签页,输入以下命令
  ```Javascript
  document.getElementById('user_name').value = 'CUAdmin'; 
  document.getElementById('password').value = 'CUAdmin'; 
  document.getElementById('loginfrm').submit();
  ```
  回车即可,三行命令搞定