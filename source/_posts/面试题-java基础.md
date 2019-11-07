---
title: 面试题-java基础
date: 2019-11-07 08:12:07
tags: 面试题
---
1.interface与abstract区别
&nbsp;&nbsp;&nbsp;&nbsp;①抽象类可以有构造方法，接口不能有构造方法
&nbsp;&nbsp;&nbsp;&nbsp;②抽象类可以有普通成员变量，接口没有普通成员变量
&nbsp;&nbsp;&nbsp;&nbsp;③抽象类可以包含非抽象的普通方法，接口的所有方法必须都是抽象的
&nbsp;&nbsp;&nbsp;&nbsp;④抽象类的抽象方法的访问类型可以是public、protected，接口的方法只能是public类型的（默认为public abstract）
&nbsp;&nbsp;&nbsp;&nbsp;⑤抽象类可以包含静态方法，接口不能包含静态方法
&nbsp;&nbsp;&nbsp;&nbsp;⑥抽象类与接口中都可以包含静态成员变量，抽象类的静态成员变量的访问类型可以是任意的，但接口定义的变量只能是public abstract final类型
&nbsp;&nbsp;&nbsp;&nbsp;⑦一个类可以实现多个接口，但只能继承一个抽象类
<!-- more -->
2.IOC的优缺点
&nbsp;&nbsp;&nbsp;&nbsp;IOC：控制反转，将控制权（创建对象和对象之间的依赖关系的权利）交给spring容器。
&nbsp;&nbsp;&nbsp;&nbsp;优点：实现组件之间的解耦，提高程序的灵活性和可维护性
&nbsp;&nbsp;&nbsp;&nbsp;缺点：①创建对象的步骤变复杂了，不直观，当然这是对不习惯这种方式的人来说。②因为使用反射来创建对象，所以在效率上会有些损耗。③缺少IDE重构的支持，如果修改了类名，还需在XML文件中手动修改
3.JVM的结构
&nbsp;&nbsp;&nbsp;&nbsp;JVM由类加载子系统，执行器引擎，本地方法库和内存空间组成。
&nbsp;&nbsp;&nbsp;&nbsp;内存空间：堆（线程共有）、方法区（线程共有）、虚拟机栈（线程私有）、本地方法栈（线程私有）、程序计数器（线程私有）
4.JVM的工作原理
&nbsp;&nbsp;&nbsp;&nbsp;首先java程序经过编译，生成class格式文件，而这个class文件就是我们虚拟机需要的，虚拟机通过加载class文件来运行我们的java程序。
5.Object有哪些方法
&nbsp;&nbsp;&nbsp;&nbsp;clone：保护方法，实现对象的浅复制，只有实现Cloneable接口才可以调用该方法，否则抛出CloneNoSupportedException异常
&nbsp;&nbsp;&nbsp;&nbsp;equals
&nbsp;&nbsp;&nbsp;&nbsp;hashcode：用于哈希查找
&nbsp;&nbsp;&nbsp;&nbsp;getClass
&nbsp;&nbsp;&nbsp;&nbsp;wait：使当前线程等待该对象的锁，当前线程必须是该对象的拥有者，也就是具有该对象的锁。wait()方法一直等待，直到获得锁或者中断。wait(long timeout)设定一个超时间隔，如果规定时间内没有获得锁就返回
&nbsp;&nbsp;&nbsp;&nbsp;notify：唤醒在该对象上等待的某个线程
&nbsp;&nbsp;&nbsp;&nbsp;notifyAll：唤醒在该对象上等待的所有线程
&nbsp;&nbsp;&nbsp;&nbsp;toString
6.Arraylist、LinkedList、Vector区别
&nbsp;&nbsp;&nbsp;&nbsp;ArrayList：底层数据结构是数组，查询快，增删慢，线程不安全，效率高
&nbsp;&nbsp;&nbsp;&nbsp;Vector：底层数据结构是数组，查询快，增删慢，线程安全，效率低
&nbsp;&nbsp;&nbsp;&nbsp;LinkedList：底层数据结构是链表，查询慢，增删快，线程不安全，效率高
&nbsp;&nbsp;&nbsp;&nbsp;查询多用ArrayList，增删多用LinkedList，如果都多用ArratList
7.String、StringBuffer、StringBuilder区别
&nbsp;&nbsp;&nbsp;&nbsp;String：适用少量的字符串操作的情况
&nbsp;&nbsp;&nbsp;&nbsp;StringBuilder：适用单线程下的字符缓冲区进行大量操作的情况
&nbsp;&nbsp;&nbsp;&nbsp;StringBuffer：适用多线程下的字符缓冲区进行大量操作的情况
&nbsp;&nbsp;&nbsp;&nbsp;运行速度/执行速度：StringBuilder>StringBuffer>String.String慢的原因：String是字符串常量，一旦创建不可更改
&nbsp;&nbsp;&nbsp;&nbsp;线程安全:StringBuffer是线程安全，StringBuilder是线程不安全
8.Map、Set、List、Queue、Stack概括
&nbsp;&nbsp;&nbsp;&nbsp;Collection是对象集合，Collection有两个子接口：List和Set
&nbsp;&nbsp;&nbsp;&nbsp;List可以通过下标取值，值可以重复
&nbsp;&nbsp;&nbsp;&nbsp;Set只能通过游标取值，并且值是不能重复的
&nbsp;&nbsp;&nbsp;&nbsp;ArraylList、Vector、LinkedList是List实现类
&nbsp;&nbsp;&nbsp;&nbsp;Map是键值对集合，HashTable和HashMap是Map的实现类，HashTable是线程安全，不能储存null值，HashMap是线程不安全，可以存储null值
&nbsp;&nbsp;&nbsp;&nbsp;Srack类：继承自Vector，实现一个后进先出的栈，提供几个基本的方法：push、pop、peak、empty、search等
&nbsp;&nbsp;&nbsp;&nbsp;Queue接口：提供几个基本方法：offer、poll、peek等，已知实现类有LinkedList、PriorityQueue等
9.TreeMap、HashMap、LinkedHashMap的区别
&nbsp;&nbsp;&nbsp;&nbsp;LinkedHashMap可以保证HashMap集合有序，存入的顺序和取出的顺序一致
&nbsp;&nbsp;&nbsp;&nbsp;TreeMap实现SortMap接口，能够把它保存的记录根据键排序，默认是键值得升序排序，也可以指定排序的比较器，当用Iterator遍历TreeMap时，得到的记录是排过序的
&nbsp;&nbsp;&nbsp;&nbsp;HashMap不保证排序，即为无序的，具有很快的访问速度。HashMap最多只允许一条记录的键为null；允许多条记录的值为null；HashMpa不支持线程的同步
&nbsp;&nbsp;&nbsp;&nbsp;在开发过程中使用HashMap比较多，在Map中插入、删除和定位元素，HashMap是最好的选择
&nbsp;&nbsp;&nbsp;&nbsp;如果要按自然顺序或自定义顺序排序遍历键，那么TreeMap会更好
&nbsp;&nbsp;&nbsp;&nbsp;如果需要输出的顺序和输入的相同，那么LinkedHashMap可以实现
&nbsp;&nbsp;