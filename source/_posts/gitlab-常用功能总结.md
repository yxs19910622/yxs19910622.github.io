---
title: gitlab-常用功能总结
date: 2018-09-03 11:58:28
tags: 
- gitlab
categories: 
- git
---
### gitlab是仿照github做出来的,但是 gitlab 拥有更多的功能:

#### 通过组群对用户进行管理
利用分组（Groups）管理权限，有时候项目比较大，往往一个项目下包含多个开发工程，如果分别给参与这些工程的人员进行授权的话，比较繁琐，而利用Groups分组的功能，可以将若干个项目成员放入同一个分组，这样此分组的git工程将自动继承分组的权限设置，只需要设置一次即可，如果有特例仍然可以在具体的git工程下进行特殊设置，比较灵活。
![](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20180903-111407.png)
<!-- more -->
#### 代码片段分享(Snippets)
允许用户分享一个project的部分代码,而不是整个项目.每个人都可以将自己常用的代码片段保存到系统并分享给大家,比自己留在本地电脑上要方便很多,而且能发挥这些片段的最大价值.
![](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20180903-111043.png)

#### 权限管理:
 *  分支保护,它们允许用户设置project的获取权限,所以一个团队中只有特定的人可以pull,push或者删除一个分支的代码.
 *  Authentication levels更进一步的提升安全性，允许用户给人读写以外的权限。举例来说，你可以给一个组员跟踪变动的权限却不给他获取代码的权限。
 
#### Work In Progress:
在发起合并请求时,开发者通过打上'WIP(仍在进行中)'状态标签让其他成员知道代码没有完成,从而阻止未完成的代码合并到其他的代码中
![](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20180903-112406.png)

#### 邮件功能
邮件是GitLab不得不配置的一块,它提供了代码提交提醒,用户密码找回等功能.
通过邮件我们可以对其他成员的操作做出及时的响应,比如新发起的Merge Requests,新的讨论信息等等.

#### Merge Requests中的Discussion:
利用讨论(Discussion)进行代码评审(code review),每当有人进行了提交之后,在系统的信息流上都可以看到这个提交的具体改动,作为项目技术负责人可以及时的了解提交情况,并针对此次提价的代码修改内容进行评论,可以细化到每一行,评论的信息系统会自动发送邮件给相关负责人,可以重复利用这个特性来做代码评审.
![](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20180903-145225.png)

#### webhook: 
webhook是一个web自定义回调函数,当程序出现特定行为时会调用指定的url.
在gitlab中一般会搭配jenkins使用,目的是在提交代码至指定分支或tag后,自动触发jenkins自动构建项目并发布到测试环境,从而避免重复性的人肉部署,实现持续集成
![](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/WX20180903-115330.png)

#### DevOps: 
新版本的gitlab更新重点放在简化内部构建的持续交付流程,提供自动化的服务上.
这是一个原本就构建在系统内服务,提供完整的CI/CD功能,其涵盖了整个端到端代码的生命周期,让开发者只需进行提交代码的动作,接下来的工作便由系统接手,包括构建、测试、代码品质扫描、安全性扫描、授权扫描、封装、效能测试、部署,一直到应用上线后,Auto DevOps还会监控执行状态.
![](https://snake-blog-pic.oss-cn-hangzhou.aliyuncs.com/auto-devops.png)

