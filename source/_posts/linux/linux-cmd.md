---
title: linux命令笔记
date: 2018-08-22 16:45:41
tags: [linux, shell-cmd]
category: linux
---

# 一些用过的命令笔记

---

## 修改默认shell
```
➜  ~ cat /etc/shells 
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/bin/rbash
/bin/dash
/bin/zsh
/usr/bin/zsh

➜  ~ chsh -s /bin/bash [user]
```

---

## scp 远程拷贝文件
从远程拷贝到本地:  
`scp lenchu@lenchu.cn:/home/lenchu/test.txt /home/lenchu/`
从本地拷贝到远程: 
`scp /home/lenchu/test.txt lenchu@lenchu.cn:/home/lenchu/`
