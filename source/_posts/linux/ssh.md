---
title: ssh免密登录
date: 2018-08-23 09:06:03
tags: linux
category: linux
---

## 先在本机生成密钥
`ssh-keygen -t rsa`

## 然后将公钥复制到目标主机的相应用户
`ssh-copy-id -i ~/.ssh/id_rsa.pub [用户]@[目标主机地址]`

## 最后就可以免密登录了
`ssh [用户]@[地址]]`