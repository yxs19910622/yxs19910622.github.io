---
title: 使用certbot搭建免费的https
date: 2019-07-06 12:54:17
tags:
---

## 为什么需要https

http协议下,信息是明文传输,不提供加密,传输被拦截时,可直接读懂其中的信息,存在安全隐患.
而https就是为了解决这一缺陷,再http基础上加入了ssl协议,依靠证书来验证服务器身份,并未与浏览器之间的通讯加密.
现在都网站基本上都是用了https加密

<!-- more -->

## 免费的ca证书

https加密必不可少的就是ca证书
由于https涉及到中间机构的校验,很多ca证书提供商都是收费的,当然其中也有免费提供的
Let’s Encrypt就是其中之一的免费提供商,他使用ACME协议来申请SSL证书,申请的时候需要验证服务器端所有权和管理权,保证了SSL证书获取途径的唯一性和安全性

## Certbot

Certbot是官方出的一个ACME客户端,大大的简化了申请流程,达到快速部署的目的
部署流程:
进入 https://certbot.eff.org/ 选择你当前使用的web服务器和操作系统
这里以nginx和centos7为例

[![img](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190717-114103@2x.png)](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190717-114103@2x.png)

选择之后下方会出现对应的操作流程,按照上面的流程基本上可以很顺利的完成操作
首先我们确保环境符合需求 centos7需要安装epel

```
yum install epel-release
yum -y install yum-utils
yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
```

最后执行安装:

```
sudo yum install certbot python2-certbot-nginx
```

成功之后就可以进行证书申请与安装了:

```
sudo certbot --nginx
```

在这之前要保证你的域名已经正确的配置在nginx中

第一步会读取nginx中配置的域名 让你选择一个或多个

[![img](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190717-115647.png)](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190717-115647.png)

第二步会询问你是否帮你把http请求重定向到https 一般都会选择是

[![img](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190717-115737.png)](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190717-115737.png)

到这里就成功了 可以访问自己的域名,看看有没有加上证书

[![img](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190717-120202.png)](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190717-120202.png)

## 证书更新

由于Let’s Encrypt的证书有效期只有90天,而在剩余时间小于一个月时才可以更新,所以官方建议使用crontab自动更新

```
echo "0 0,12 * * * root python -c 'import random; import time; time.sleep(random.random() * 3600)' && certbot renew" | sudo tee -a /etc/crontab > /dev/null
```

当然你也可以选择使用`certbot renew`命令来手动更新

## 遇到的问题

[![img](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190716-203727@2x.png)](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190716-203727@2x.png)

一开始以为是模块缺失 后来才发现是版本问题

```
pip list 2>/dev/null | grep requests
rpm -q python-requests --queryformat '%{VERSION}\n'
```

以上两个命令查询出的版本不一致 重装即可

```
pip install --upgrade --force-reinstall 'requests==2.6.0'
```

[![img](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190716-203747@2x.png)](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20190716-203747@2x.png)

这里也是版本问题 重新安装合适的版本