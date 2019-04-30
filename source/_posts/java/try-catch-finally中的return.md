---
title: try-catch-finally中的return
date: 2019-04-29 22:00:48
category: Java
tags: Java
---

## try-catch-finally

try: 正常执行的语句

catch: try中抛出相应的异常时执行的语句

finally: 不管执行的是try还是catch, 最终都会执行的语句

先回顾以上基础

## AutoClose

从jdk1.7开始, 可以在try后面加一对圆括号, 把要关闭的资源放到圆括号里声明, 可以自动关闭

```java
File f = new File("test");
try (
    InputStream is = new FileInputStream(f);
    InputStreamReader isr = new InputStreamReader(is);
    BufferedReader br = new BufferedReader(isr);
) {
    br.readLine();
} catch (Exception e) {
}
```

**自动关闭的资源需要实现AutoCloseable接口**

实际上`AutoCloseable`接口是jdk1.7新增的, 而`Closeable`接口继承自`AutoCloseable`, 所以只要实现`Closeable`接口即可

## 带有`return`语句的try-catch-finally

```java
public static int tryCatchFinally() {
    int a = 1, b = 2;
    try {
      return ++a + b;
    } catch(Exception e) {
      return a;
    } finally {
      return b;
    }
}
```

使用`javap`反编译字节码, 部分如下

```shell
Code:
  stack=1, locals=3, args_size=0
      0: iconst_1
      1: istore_0
      2: iconst_2
      3: istore_1
      4: iinc          0, 1
      7: goto          15
    10: astore_2
    11: goto          15
    14: pop
    15: iload_1
    16: ireturn
  Exception table:
      from    to  target type
          4    10    10   Class java/lang/Exception
          4    14    14   any
```

大致意思: 

如果代码4到10(try中的语句)发生异常(catch中捕获的异常)就跳转到10继续运行

如果代码4到14(try和catch)发生异常(任何异常)就跳转到14继续运行

如果不发生异常, 会在7执行goto, 跳转至15

或者try发生异常被catch捕获, 那就会在11执行goto, 跳转至15

所以最终执行的return语句是finally中的return

## try-catch中有`return`, finally中无`return`

```java
int a = 1, b = 2;
try {
    return a;
} catch(Exception e) {
    return b;
} finally {
    a = 10;
    b = 20;
}
```

在finally中对a和b赋值, 在try和catch中返回a和b

结果return的a和b是1和2, 赋值语句也确实执行了, 只是未能对返回结果造成影响

具体什么情况呢? 还是看字节码

```shell
Code:
  stack=1, locals=5, args_size=0
    0: iconst_1
    1: istore_0
    2: iconst_2
    3: istore_1
    4: iload_0
    5: istore        4
    7: bipush        10
    9: istore_0
    10: bipush        20
    12: istore_1
    13: iload         4
    15: ireturn
    16: astore_2
    17: iload_1
    18: istore        4
    20: bipush        10
    22: istore_0
    23: bipush        20
    25: istore_1
    26: iload         4
    28: ireturn
    29: astore_3
    30: bipush        10
    32: istore_0
    33: bipush        20
    35: istore_1
    36: aload_3
    37: athrow
  Exception table:
    from    to  target type
      4     7    16   Class java/lang/Exception
      4     7    29   any
      16    20    29   any
```

与上次不同的是: 这次的字节码没有goto指令

两个`ireturn`指令把把这段逻辑分成了三部分, 分别对应try, catch, 和未catch的异常情况

finally中的语句都在`ireturn`或`athrow`之前被执行

至于为什么finally中的语句未对返回值造成影响, 三段也都一样, 这里挑try中的指令来看一下

```shell
4: iload_0            // 把0位置的变量(a)值推入栈顶
5: istore        4    // 把栈顶值存入4位置变量
7: bipush        10   // 把单字节常量值10推入栈顶
9: istore_0           // 把栈顶值赋给0位置变量(a)
10: bipush        20
12: istore_1          // 同上
13: iload         4   // 把4位置变量值推入栈顶
15: ireturn           // 返回栈顶值
```

总结一下就是: 当在try或catch中返回将在finally中要修改的基本类型变量时, 虚拟机会缓存将要返回的值, 再执行finally中的语句修改变量

## 为什么要这样设计呢

感觉这个有点类似上一篇`内部类访问局部变量为什么必须要用final修饰`中的原因

> 为了消除代码的不确定性, 保持代码的简单易懂
