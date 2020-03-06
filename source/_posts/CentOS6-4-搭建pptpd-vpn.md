---
title: CentOS6.4 搭建pptpd vpn
date: 2014-06-26 17:11:44
tags: 
- linux
categories:
- linux
---
又重装了服务器,vpn也要重新搭建了,不过都忘了怎么装了,捯饬了半天

需求:143是服务器,145是数据库,他们之间用局域网连接,145没连外网,操作数据库需要先用vpn连接143,再访问145
<!--more-->
网上找了几个版本,最后发现这个一键安装的很好用

wget http://www.hi-vps.com/shell/vpn_centos6.sh
chmod a+x vpn_centos6.sh
bash vpn_centos6.sh

出现3个选项  输入1回车是安装
最后提示安装成功,并显示账号密码

之后进入 /etc/pptpd.conf
修改ip:
localip 192.168.101.17 //这里填的是143的内网ip地址
remoteip 192.168.101.1-16 //这里是给vpn连接的电脑分配的ip段

关于/etc/ppp/options.pptpd里的dns,我没有动

最后设置转发
iptables -t nat -A POSTROUTING -s 192.168.101.1/16 -o eth1 -j MASQUERADE
地址段对应上面那一段 eth1是我内网的网卡


之后在自己的电脑上建立vpn连接,连接到143, ping 192.168.101.18(数据库内网地址),通了

最后用plsqldev连数据库 可用 over