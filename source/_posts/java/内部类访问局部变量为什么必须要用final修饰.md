---
title: 内部类访问局部变量为什么必须要用final修饰
date: 2019-04-21 13:14:31
category: Java
tags: Java
---

**内部类访问局部变量为什么必须要用final修饰**

看了大概五六篇博客, 讲的内容都差不多, 讲的内容也都很对, 但我觉得有些跑题了

略叙一下

```java
String s = "hello";
class Inner implements Runnable {
	public void run() {
		System.out.println(s);
	}
}
// s = "world";

Thread t = new Thread(new Inner());
t.start();
```

- jdk1.8及后续版本可以不显式声明final

所以以上代码在Java8是可以编译运行的, 但是如果在Inner类声明后再对s赋值, 就会报此错误
`Local variable s defined in an enclosing scope must be final or effectively final`

所以, 尽管Java8以后不用显式声明为final, 但是内部类引用的变量实际上还是final的

- 内部类中存在要用到的外部变量的引用, 存于内部类成员变量中, 在内部类构造时传入

这点可以通过反射或者直接查看字节码证实

内部类中有了所需变量的引用, 那么在方法执行完之后, 依然可以访问到此变量

可以说是摆脱了方法内部变量作用域对内部类变量使用的限制
(啰嗦解释一句, 如果内部没有保存引用, 那么方法执行完毕, 方法内所有本地变量随方法所在栈帧销毁, 内部类将无法获取这一引用)

- 为了保证内部的引用与外部的引用指向一致, 所以必须是final

这一点最接近题意

但是他们大多只解释了为了使内部与外部保持一致

- 我的理解

使内部与外部保持一致不是必要的

```java
public static void main(String[] args) {
	String name = "lengchu";
	class Inner {
		String name;

		Inner(String name) {
			this.name = name;
		}

		void sayHi() {
			System.out.println("hello " + name);
		}
	}

	name = "lenchu";
	new Inner(name).sayHi();
}
```

开头那段代码编译成字节码后和这段其实差不多

内部的name字段和构造器是编译器为我们加上的, 实例化的时候构造器里的值也是编译器给我们传过去的

如果把编译器为我们做的这些我们自己手动实现了, 再来看这个所谓的`使内部与外部保持一致`的需求是多么的无理, 完全就没必要嘛

- 所以到底为什么呢

个人理解

为了消除代码的不确定性, 保持代码的简单易懂

怎么说呢? 我们把上面实例化和调用那段代码稍作修改

```java
Inner i = new Inner(name);
name = "lenchu";
i.sayHi();
```

因为构造器和内部字段是我们自己实现的, 所以我们可以轻松判断此时修改name对i的值是没有影响的

但是如果我们不自己实现这些呢

```java
public static void main(String[] args) {
	String name = "lengchu";
	class Inner {
		void sayHi() {
			System.out.println("hello " + name);
		}
	}

	Inner i = new Inner();
	// name = "lenchu";
	i.sayHi();
}
```

假如可以对name重新赋值

那么name运行的时候name到底是谁呢

从上面的分析我们可以知道name的值应该是内部类实例化时的值

但是从执行顺序上看难免多少会有些歧义

干脆就不让他改了吧--!(个人见解不代表官方意见)

---

**强行对比一波js的上下文环境**

```javascript
function fn() {
    let i = 0
    let ret = function() {
        return i++
    }
    return ret
}

let i = 10
let inc = fn()
inc() // 0
```

Java的运行是基于方法栈, 而js则是基于执行上下文栈

二者有很多相似之处, 略叙一下

Java的每个方法调用都会往方法栈里推入一帧, 
这一栈帧里存放着方法内部声明的局部变量, 方法参数等,
方法执行完毕时, 栈帧出栈, 栈帧里的内容随之销毁

js的上下文栈也与之类似

- js的栈帧未必会随着上下文执行完毕而销毁

如上例, 
fn执行完毕, 他的上下文就不会销毁, 
如果销毁, 那么ret执行的时候变量i就找不到其所指了

栈帧销毁, 变量找不到引用, 像极了Java方法栈帧销毁, 内部类变量找不到引用

- 栈帧未必销毁, 那怎么知道当前上下文所用的帧呢

标记, 标记当前上下文所用的栈帧为active

同时还会标记每一帧的上一帧, 
这样就以另一种方式解决了类似的问题
