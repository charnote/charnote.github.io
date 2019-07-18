---
layout: post
title: Java 中 equals 与 == 的区别
date: 2019-07-17 17:09:00 +0800
---


项目开发过程中，我们可能经常需要重写 Object 类中 equals 方法，但我们知道 == 也是用于比较，因为容易混淆它们俩之间的区别，所以花时间写了一篇文章说明它们之间的差异。

:)

若有不正之处，不吝指出；

Java 中数据类型可分为两大类：基本数据类型与引用(复合)数据类型；

#### == 符号含义

基本数据类型比如 int，char，byte，long，short 等。基本数据类型比较可使用 == 去比较值；

```java
int a = 1;
int b = 1;
System.out.println(a == b);//true
```

引用数据类型比较就相对于基本数据类型会让人困惑些许！

当使用 == 用于引用数据类型之间比较的是变量在内存中的地址；

```java
//使用 new 关键字去创建对象就会在内存中申请一块空间！
Object obj1 = new Object();//在内存中申请一块空间
Object obj2 = new Object();//在内存中申请一块空间
System.out.println(obj1 == obj2);//false

//这时 obj1 与 obj3 在内存中使用的是同一块空间
Object obj3 = obj1;//将 obj1 内存地址引用赋值给 obj3
System.out.println(obj3 == obj1);//true
```

#### equals() 方法

equals() 方法在 Object 类中，位于 java.lang 包中，Object 是 Java 中所有类的基类，开发中我们会经常重写像是 equals()，hashCode()，toString() 方法；

说到 equals 方法，我们首先可以看一下 JDK 源码：

```java
public boolean equals(Object obj) {
        return (this == obj);
    }
```

我们可以看到它实质上就使用 == 去比较，前面我们说到引用数据类型使用 == 比较的是地址值；Object 类中 equals() 方法默认也是比较地址值；

```java
Object obj1 = new Object();
Object obj2 = new Object();
System.out.println(obj1 == obj2);//false
Object obj3 = obj1;
System.out.println(obj3.equals(obj1));//true
```

== 与默认 equals() 方法都是比较地址值，但如果我们不想比较它们之间的地址值，我们想比较两个字符串对象的内容值是否相同该怎么做呢？对的！就是重写 equals() 方法。像是为了比较对象内容值，String，Interger 等类都重写了 equals() 方法。

```java
String s1 = "java";
String s2 = "java";
System.out.println(s1 == s2);//true
System.out.println(s1.equals(s2));//true
```

equals() 方法比较的是对象的内容值，结果是 true 可以理解；但为什么使用 == 去比较也是 true 呢？上面说到 == 用于引用数据类型比较的是地址值，难道 s1 与 s2 在内存中都共用同一内存地址？Java 程序中字符串常量被创建时即被添加到共享池中，当在程序中再次创建内容相同的字符串常量时就会共享上次创建好的数据也就是共享同一内存地址。所以上面例子中就直接把这个字符串对象的地址引用赋值给上述代码中的 s2，所以实际上 s1 与 s2 是共用同一对象（地址值相同），使用 == 比较结果是 true 就比较好理解了！

我们再看一个例子！

``` java
String s3 = "freedom";
String s4 = new String("freedom");
System.out.println(s3 == s4);//false
System.out.println(s3.equals(s4));//true
```

这次为什么使用 == 比较结果是 false 呢？因为只要使用 new 关键字去创建对象都会在内存中申请一块空间。

理解了 == 比较，那就让我们看一下 String 类中重写的 equals() 方法：

```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String aString = (String)anObject;
            if (coder() == aString.coder()) {
                return isLatin1() ? StringLatin1.equals(value, aString.value)
                                  : StringUTF16.equals(value, aString.value);
            }
        }
        return false;
    }
```

我们看到 equals() 方法接收一个 Object 类型的参数，根据数据的类型调用对应的 equals() 方法。就拿上面 s3 与 s4 这两个对象举例，方法首先会判断这两个数组长度是否相同，否则返回 false，相同会循环判断数组中数据是否相等，相等返回 true，认定这两个对象内容值相等。感兴趣的朋友也可以去看一下 Integer 等类是怎么重写该方法的实现它特有的比较方式的。

:)
