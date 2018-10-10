---
title: linux 文件监控设置
date: 2018-10-10 21:36:02
tags: linux
category: linux
---

## 检查现有文件监控数量
`tac /proc/sys/fs/inotify/max_user_watches `

## 修改文件监控数目
- 打开配置文件
`sudo vi /etc/sysctl.conf `
- 添加配置
`fs.inotify.max_user_watches=[数量]`

## 使配置生效
`sudo sysctl -p`


## 参考
> https://blog.csdn.net/davidhopper/article/details/79620425
