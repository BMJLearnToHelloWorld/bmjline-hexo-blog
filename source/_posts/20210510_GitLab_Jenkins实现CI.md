---
title: GitLab + Jenkins实现CI
description: 在每次提交活着合并代码之后，可以实现自动化的构建、自动化测试等，大大省去了合并代码之后开发人员的重复手动构建。
date: 2021-05-10 22:11:52
tags:
- GitLab
- Jenkins
- build
- CI
categories: 
- Technology
---

Jenkins设置Build Triggers
-----------------------

#### 目的是为了能够使jenkins被触发

*   在工程下点击configure
    

<!-- ![](http://52.72.99.86:9000/2021/05/jenkins-configfa28345c99f04c2d84868c953c0bc3dd.png) -->

*   按以下选择进行勾选（基本为默认设置），点击Advanced...
    

<!-- ![](http://52.72.99.86:9000/2021/05/jenkins-build-triggers45ef685042674ce8a01c970c5207fb23.png) -->

*   点击Generate，会生成一个Secret token，这个token将被作为触发jenkins的认证，后面需要将这个token配置在GitLab中
    

<!-- ![](http://52.72.99.86:9000/2021/05/jenkins-build-triggers-advancedb79fe4d166d84dc6a327f23477fe972c.png) -->

#### Jenkins配置部分结束

* * *

GitLab设置
--------

#### 首先设置jenkins访问权限，确保jenkins有权限pull代码，才能build

*   用admin账号登录GitLab，并点击账号设置下的Access Tokens
    

<!-- ![](http://52.72.99.86:9000/2021/05/access-token7f66e9ec014c48bba079ddbe38eb8eb6.png) -->

*   点击Create personal access token
    

<!-- ![](http://52.72.99.86:9000/2021/05/access-token-create24d12b35622b4c78a964b70933fbe323.png) -->

*   可以设置有效期限，再勾选api权限
    

<!-- ![](http://52.72.99.86:9000/2021/05/access-token-apibe8fbfafc5a2460db2315729a37df137.png) -->

*   点击create，这时就会有一条access token，记录下access token 并配置到jenkins全局设置中
    

<!-- ![](http://52.72.99.86:9000/2021/05/access-token-active2bbf6bb0909f4d2a9c13e7ead16965b6.png) -->

#### 代码提交在GitLab上，设置提交时的触发条件（比如在提交到dev分支时触发），去触发jenkins build

*   进入需要配置CI的项目，点击settings下的Webhooks
    

<!-- ![](http://52.72.99.86:9000/2021/05/gitlab-webhook6dca80d1414d4c95afe1941cbec896bd.png) -->

*   将jenkins项目地址与在jenkins生成的Secret token填入，勾选Push events（表示在GitLab触发了push是会调用jenkins build），也可以输入想要触发的分支（其他分支push将不会触发jenkins build）
    

<!-- ![](http://52.72.99.86:9000/2021/05/gitlab-webhook-config-1eb77d389b80044b881ab2065e29d3f57.png) -->

*   点击 Add webhook
    

<!-- ![](http://52.72.99.86:9000/2021/05/gitlab-webhook-config-2573dc5380b4e40249671cad788ce8f9c.png) -->

*   可以看到已经激活了webhook
    

<!-- ![](http://52.72.99.86:9000/2021/05/gitlab-webhook-config-3d7e33a8d2c11453e8f91d2c670fd222a.png) -->

#### GitLab配置部分结束

* * *

### CI持续集成（Continuous Integration）

在每次提交活着合并代码之后，可以实现自动化的构建、自动化测试等，大大省去了合并代码之后开发人员的重复手动构建。本篇只介绍了CI的自动化构建部分。

### CD持续交付 / 部署（Continuous Delivery / Continuous Deployment）

当然还有CD，作为CI的后续工作。

例如，如果项目的部署方式为docker部署并且将部署包做了外部的文件映射，那么在jenkins的build脚本的最后添加docker的restart命令，那么在开发人员提交完代码之后，不用做任何的操作，几分钟之后便能让代码生效了。