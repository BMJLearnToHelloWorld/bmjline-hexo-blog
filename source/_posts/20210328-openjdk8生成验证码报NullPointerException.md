---
title: openjdk8生成验证码报NullPointerException
description: openjdk缺少了字体库，这需要在生成base64图片时需要，所以报错了。
date: 2021-03-28 23:06:09
tags:
- Java
- Docker
- openjdk
categories: 
- Technology
---

## 起因
想在登录页面添加验证码，需要在后端生成随机数并转为base64返回前端。
于是我参考了 [jeecg-boot](https://github.com/zhangdaiscott/jeecg-boot) 后端生成base64的做法，但是用Dockerfile build后调用接口会报出空指针异常，报错的log如下：
`java.lang.NullPointerException:null at sun.awt.FontConfiguration.getVersion(FontConfiguration.java:1264)`

***

## 解决办法
在Dockerfile里添加：
`RUN apk add --update font-adobe-100dpi ttf-dejavu fontconfig`

***

## 原因
我的Dockerfile使用的是 `openjdk:8-jdk-alpine`
jeecg使用的是 `anapsix/alpine-java:8_server-jre_unlimited` ，是OracleJDK

openjdk缺少了字体库，这需要在生成base64图片时需要，所以报错了。

加上了 `ttf-dejavu fontconfig` 这些包之后，重新build镜像，便可以成功生成base64了。

***

## 参考
[https://blog.csdn.net/weixin_39025362/article/details/106099249](https://blog.csdn.net/weixin_39025362/article/details/106099249)
