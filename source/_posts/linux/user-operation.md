---
title: 用户管理命令笔记
date: 2018-08-22 16:32:32
tags: linux
category: linux
---

### 增加用户: 
`useradd -d /home/username -m username`

### 为用户添加密码: 
`passwd username`

### 为用户添加组: 
`usermod -G groupname username`

### 删除用户: 
`userdel -rf username`
*-rf 是同时删除用户的家*
