---
title: linux命令笔记
date: 2018-08-22 16:45:41
tags: linux shell-cmd
category: linux
---

# 一些用过的命令笔记

### 修改默认shell
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
