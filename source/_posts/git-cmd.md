---
title: git命令笔记
date: 2018-08-12 12:05:04
tags: git
category: git
---

## 查看本地文件修改内容
`git diff fileName`

## 使用多个远程仓库

#### 添加远程仓库
`git remote add origin git@github.com:lengchu/novel-java.git`

#### 为远程仓库添加地址
`git remote set-url --add origin git@gitee.com:lenchu/novel-java.git`

#### 或者定义多个远程仓库
`git remote add gitee git@github.com:lengchu/novel-java.git`