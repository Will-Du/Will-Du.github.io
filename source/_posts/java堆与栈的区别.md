---
title: java堆与栈的区别
date: 2020-06-23 16:31:14
tags: Java
---
<b style="color: orangered">堆</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1.Java的堆是一个运行时数据区，类的对象从堆中分配空间。</b>这些对象通过new等指令建立，通过垃圾回收器来销毁。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2.堆的优势是可以动态地分配内存空间，</b>需要多少内存空间不必事先告诉编译器，因为它是在运行时动态分配的。但缺点是：由于需要在运行时动态分配内存，所以存取速度较慢。
<b style="color: orangered">栈</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1.栈中主要存放一些基本数据类型的变量(byte/short/int/long/float/double/boolean/char)和对象的引用。</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2.栈的优势是，存取速度比堆快，栈数据可以共享。缺点是：存放在栈中的数据占用多少内存空间需要在编译时确定下来，缺乏灵活性。</b>
<!-- more -->&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;举例说明栈数据可以共享：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;String可以用以下两种方式来创建：
```java
String str1 = new String("abc");
String str2 = "abc"
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;第一种使用new来创建对象，它存放在堆中。每调用一次就创建一个新的对象。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;第二种是先在栈中创建对象的引用str2，然后查找栈中有没有存放“abc”，如果没有，则将“abc”存放进栈，并将str2指向“abc”，如果已经有“abc”，则直接将str2指向“abc”。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;下面用代码说明上面的理论：
```java
public void static main(String[] args) {
    String str1 = new String("abc");
    String str2 = new String("abc");
    System.out.println(str1 == str2);// flase
}
```
```java
public void static main(String[] args) {
    String str1 = "abc";
    String str2 = "abc";
    System.out.println(str1 == str2);// true
} 
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因此，应第二种方式创建多个“abc”字符串，在内存中其实只存在一个对象而已。这种写法有利于节省内存空间。同时还可以提高程序的运行速度，因为JVM会自动根据栈中数据的实际情况来决定是否创建新对象。