---
title: int和Integer区别
date: 2020-04-06 19:02:12
tags: 面试题
---
<b style="color:orangered">int和Integer区别</b>
&nbsp;&nbsp;&nbsp;&nbsp;1.Integer是int的包装类，int则是java的一种基本数据类型。
&nbsp;&nbsp;&nbsp;&nbsp;2.Integer变量必须实例化后才能使用，而int变量不需要。
&nbsp;&nbsp;&nbsp;&nbsp;3.Integer实际是对象的引用，当new一个Integer时，实际上是生成一个指针指向此对象；而int则是直接存储数据值。
&nbsp;&nbsp;&nbsp;&nbsp;4.Integer默认值是null，int默认值是0。
<!-- more -->
<b style="color:orangered">关于Integer和int的比较</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">1.由于Integer变量实际上是对一个Integer对象的引用，所以两个通过new生成的Integer变量永远是不相等的。(因为new生成的是两个对象，其内存地址不同)</b>
```java
Integer x = new Integer(100);
Integer y = new Integer(100);
System.err.print(x == y); // false
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">2.Integer变量和int变量比较时，只要两个变量的是相等的，其结果为true。(因为包装类Integer和基本数据类型int比较时，java会自动拆包为int，然后进行比较，实际上就变为了两个int变量的比较)</b>
```java
Integer x = new Integer(100);
int y = 100;
System.err.print(x == y); // true

Integer a = new Integer(200)
int b = 200;
System.err.print(a == b); // true
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">3.非new生成的Integer变量和new Integer()生成的变量比较时，结果为false.(因为非new生成的Integer变量指向的是java常量池中的对象，而new Integer()生成的变量指向堆中新建的对象，两者在内存中的地址不同)</b>
```java
Integer x = new Integer(100);
Integer y = 100;
System.err.print(x == y); // false
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">4.对于两个非new生成的Integer对象，进行比较时，如果两个变量的值在区间-128到127之间，则比较结果为true，如果两个变量的值不在此区间，则比较结果为false</b>
```java
Integer x = 100;
Integer y = 100;
System.err.print(x == y); // true

Integer a = 128;
Integer b = 128;
System.err.print(a == b); // false
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;java在编译Integer x = 100时，会翻译成为Integer x = Integer.valueOf(100);,而java API中对Integer类型的valueOf的定义如下：
```java
public static Integer valueOf(int i) {
  assert IntegerCache.high >= 127;
  if(i >= IntegerCache.low && i <= IntegerCache.high) {
    return IntegerCache.cache[i + (-IntegerCache.low)];	
  }
  return new Integer(i);	
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;java对于-128到127之间的数，会进行缓存，Integer i = 127时，会将127进行缓存，下次再写Integer j = 127时，就会一直从缓存中取，就不会new了。