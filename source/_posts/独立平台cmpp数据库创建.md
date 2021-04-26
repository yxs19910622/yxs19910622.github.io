---
title: certbot ACMEv2升级以及版本更新后出错
date: 2021-04-25 16:50:41
tags:
- certbot
categories:
- certbot
---

# certbot ACMEv2升级以及版本更新后出错

先是收到邮件域名快过期了

到服务器上手动更新提示:

```python
Cert is due for renewal, auto-renewing...
Plugins selected: Authenticator nginx, Installer nginx
Starting new HTTPS connection (1): acme-v01.api.letsencrypt.org
Attempting to renew cert (xxx.com) from /etc/letsencrypt/renewal/console.account.shomop.com.conf produced an unexpected error: urn:acme:error:serverInternal :: The server experienced an internal error :: ACMEv1 Brownout in Progress. ACMEv1 will fully turn off on June 1, 2021. Check https://letsencrypt.status.io/ for more details.. Skipping.
```

大概意思是Let's Encrypt证书将分阶段完全禁用ACMEv1客户端的获取方式

首先尝试更新cerbot

```shell
yum update certbot
```

完成后尝试更新

```shell
[root@iZbp13ysimf3dh7v85dg2fZ ~]# certbot renew
An unexpected error occurred:
ImportError: cannot import name constants
Please see the logfile '/tmp/tmpRetp74/log' for more details.
```

<!-- more-->

查看错误日志

```powershell
2021-04-26 10:54:27,312:DEBUG:certbot._internal.log:Exiting abnormally:
Traceback (most recent call last):
  File "/usr/bin/certbot", line 9, in <module>
    load_entry_point('certbot==1.11.0', 'console_scripts', 'certbot')()
  File "/usr/lib/python2.7/site-packages/certbot/main.py", line 15, in main
    return internal_main.main(cli_args)
  File "/usr/lib/python2.7/site-packages/certbot/_internal/main.py", line 1383, in main
    plugins = plugins_disco.PluginsRegistry.find_all()
  File "/usr/lib/python2.7/site-packages/certbot/_internal/plugins/disco.py", line 236, in find_all
    plugin_ep = cls._load_entry_point(entry_point, plugins, with_prefix=False)
  File "/usr/lib/python2.7/site-packages/certbot/_internal/plugins/disco.py", line 254, in _load_entry_point
    plugin_ep = PluginEntryPoint(entry_point, with_prefix)
  File "/usr/lib/python2.7/site-packages/certbot/_internal/plugins/disco.py", line 56, in __init__
    self.plugin_cls = entry_point.load()
  File "/usr/lib/python2.7/site-packages/pkg_resources.py", line 2260, in load
    entry = __import__(self.module_name, globals(),globals(), ['__name__'])
  File "/usr/lib/python2.7/site-packages/certbot_nginx/configurator.py", line 17, in <module>
    from certbot import constants as core_constants
ImportError: cannot import name constants
2021-04-26 10:54:27,312:ERROR:certbot._internal.log:An unexpected error occurred:
2021-04-26 10:54:27,312:ERROR:certbot._internal.log:ImportError: cannot import name constants
```

完犊子 看不懂了 网上一顿搜 说是多个certbot来源之类的 但是我只有一个安装方式

日志里提到了nginx

```powershell
[root@iZbp13ysimf3dh7v85dg2fZ ~]# rpm -qa|grep certbot
certbot-1.11.0-1.el7.noarch
python2-certbot-nginx-0.25.1-1.el7.noarch
python2-certbot-1.11.0-1.el7.noarch
```

python2-certbot-nginx版本看起来不一致

```powershell
yum update python2-certbot-nginx
```

完成后再更新

```powershell
Cert is due for renewal, auto-renewing...
Plugins selected: Authenticator nginx, Installer nginx
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org
```

看起来变成acme-v02了 总算齐活

