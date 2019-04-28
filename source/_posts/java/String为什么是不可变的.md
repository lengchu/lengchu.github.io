---
title: String为什么是不可变的
date: 2019-04-19 16:10:17
tags: Java
---

**前几天一个面试被问到`String为什么是不可变的?`, 自我感觉当时回答的不太理想, 事后总结一下**

## 不可变的是什么

我们谈论的String不可变, 指的是字符串的值不可变

例: `String s = "hello"` s的值就是`hello`, 不可变也指的是这个值不可变

类比到int基本类型就相当于`int i = 1`, 假如这里i的值不可变, 那指的就是1不可变

## 为什么不可变

众所周知Java的String类型并非基本类型, 即String是一个类

既然String是类, 那我们就深入其内部实现来一探究竟

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];

    //......
}
```

从源码来看, String类内部是用char数组来保存字符串的值, 并且char[]是final的, 这里的final意味着什么呢?

- value必须在构造时为其赋值
- 赋值后value的引用不能再变

当我们实例化一个String对象并得到其引用后, 构造已经结束了, 即value的引用已经不能再变了

那么value的值呢, 理论上是可以改变的, 只要我们拿到value的引用, 可以直接通过下标改变他的值

实际上呢?

value的值我们从String外部获取不到

- 首先, 构造的时候我们传入String的值, String内部赋值给value字段的时候都经过copy, 也就是说我们传给String的值经过构造后已经有了一份我们获取不到的备份留在了String内部, 我们改变原来的值对String内部的value已经毫无影响

```java
char[] c = new char[32];
c[0] = 'h';
String s = new String(c);
System.out.println(s);
c[1] = 'e';
System.out.println(s);
```

毫无疑问, 两次的输出都是`h`

- 其次, String类没有提供对外的接口来改变value的值, 通过查看String类源码可以看到, String类所有的公开方法中, 没有一个可以修改value的值

- 最后

```java
String s = "hello";
s = "world";
```

这种情况, s的值貌似改变了, 从`hello`变成了`world`

其实这里s所改变的是他所引用的对象, 并不是String对象的值改变了

怎么说呢? 我们这样`String s = "hello"`写代码只是一种简写, 或者称为'语法糖'

实际执行的时候是什么样子的呢? 非要用Java代码表示的话大致意思是这样`String s = String.valueOf("hello")`

嗯? 这样表述貌似也有些问题... 改天再单独总结...

这里意思就是`valueOf("hello")`会返回一个内部的value字段存的是"hello"的String对象

从上面几点分析我们知道, 拿到String对象的时候, 内部的value字段已经无法修改了

那么这里的`s = "world"`, 这个赋值又是什么意思呢?

根据上面对`valueOf`的分析, 这里的`s = "world"`赋值之后s是一个内部字段存的是"world"的String对象

又因为value的值在String构造的时候就已经指定且不可再变, 所以这个s和之前的s引用的必然不是同一个对象

综上, 对s赋值是改变了s所引用的对象, 而改变前后两个String对象既不是同一个对象, 内部的value值又不一样

所以, 直接赋值也不能改变字符串的值, 改变的只是引用

所以, String不可变. 

## 真的不可变吗?

按照上述的分析, 貌似真的不可变

因为一般情况下我们获取不到String内部的value数组的引用

那么二般情况呢

```java
char[] origin = new char[32];
origin[0] = 'h';
String s = new String(origin);
System.out.println(s);
try {
	Field f = s.getClass().getDeclaredField("value");
	f.setAccessible(true);
	Object o = f.get(s);
	if(o instanceof char[]) {
		char[] c = (char[]) o;
		System.out.println(c.length);
		c[1] = 'e';
		c[2] = 'l';
		System.out.println(s);
	}
} catch (Exception e) {
	e.printStackTrace();
}
```

利用反射我们可以直接获取类内部的属性, 挣脱了访问权限的束缚

获取了String内部的value数组, 改变了String的值

---

假如以后遇到别的改变String值的方法, 再来记录

