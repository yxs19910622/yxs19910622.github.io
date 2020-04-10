---
title: gitlab开启2FA双因子认证登录
date: 2020-04-08 10:29:04
tags:
 - gitlab
---



今天打开gitlab 发现被提示需要给root账号开启双因子验证登录

![image-20200408104033861](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/image-20200408104033861.png)

代码的重要性对互联网公司不言而喻 奥利给 干了兄弟们

<!-- more-->

## 通过一次性密码验证器启用2FA

首先打开gitlab提供的文档 这里进行了详尽的说明 手把手教学

https://docs.gitlab.com/12.6/ee/user/profile/account/two_factor_authentication.html

![image-20200408103107816](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/image-20200408103107816.png)

首先根据安装一个*OTP*应用 这里选择谷歌 因为其他的咱也不认识 安卓机直接去Google Play下载就行

![image-20200408104555384](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/image-20200408104555384.png)

安装后直接扫面之前gitlab提供的二维码 并把app显示的6位随机码输入 就成功启用了

![image-20200408103308012](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/image-20200408103308012.png)

成功后展示的是恢复代码 如果验证app出问题 可以使用其中一个进行登录(每个只能使用一次)

这个时候我们再退出重新登录 就需要验证随机密码

![image-20200408105033811](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/image-20200408105033811.png)

搞定 后面还有U2F设备支持 我们就不用了

![image-20200408103603098](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/image-20200408103603098.png)

## 个人访问令牌

在启用2FA后 就无法再使用普通帐户密码进行身份验证 必须改为使用个人令牌 这里就不再细说

