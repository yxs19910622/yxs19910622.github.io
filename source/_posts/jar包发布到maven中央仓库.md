---
title: jar包发布到maven中央仓库
date: 2020-09-18 10:57:24
tags: maven
---

## 一. 在sonatype提交新的申请

首先进入sonatype官网 https://issues.sonatype.org/secure/Dashboard.jspa 没有帐号的注册一下就可以

接着点击上方的create按钮 创建一个新的issue

<img src="https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/image-20200918110906628.png" alt="image-20200918110906628" style="zoom:50%;" />

这里创建之后就等着 审核人员有回复的话会邮件提醒 一般是要求你验证域名或者github所有权

<!--more-->

<img src="https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/image-20200918111113946.png" alt="image-20200918111113946" style="zoom:50%;" />

等到没问题了他会将issue状态置为**RESOLVED** 并提醒你成功后回来评论下

## 二. 配置maven

在settings.xml文件的servers标签下新增

```xml
  <servers>
    <server>
      <id>sonatype-nexus-snapshots</id>
      <username>sonatype账号</username>
      <password>sonatype密码</password>
    </server>
    <server>
      <id>sonatype-nexus-staging</id>
      <username>sonatype账号</username>
      <password>sonatype密码</password>
    </server>
  </servers>
```

## 三. 生成密钥

