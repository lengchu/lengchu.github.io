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
