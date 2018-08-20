---
title: '字符串操作'
date: 2018-08-20 15:10:21
tags: shell
category: shell笔记
---

### 获取字符串长度
```
➜  ~ str=hello   
➜  ~ echo ${#str}
5
➜  ~ expr length $str
5
```

### 字符串所在位置
```
➜  ~ echo $str
hello
➜  ~ expr index $str h
1
➜  ~ expr index $str l
3
➜  ~ expr index $str o
5
➜  ~ expr index $str w
0

➜  ~ expr index $str he
1
➜  ~ expr index $str el
2
➜  ~ expr index $str ll
3
➜  ~ expr index $str lo
3
```

### expr
好多字符串的操作都可以使用expr完成
详情 `man expr`
