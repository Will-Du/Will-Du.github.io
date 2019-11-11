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
&nbsp;&nbsp;&nbsp;&nbsp;查询多用ArrayList，增删多用LinkedList，如果都多用ArrayList
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
&nbsp;&nbsp;&nbsp;&nbsp;ArrayList、Vector、LinkedList是List实现类
&nbsp;&nbsp;&nbsp;&nbsp;Map是键值对集合，HashTable和HashMap是Map的实现类，HashTable是线程安全，不能储存null值，HashMap是线程不安全，可以存储null值
&nbsp;&nbsp;&nbsp;&nbsp;Srack类：继承自Vector，实现一个后进先出的栈，提供几个基本的方法：push、pop、peak、empty、search等
&nbsp;&nbsp;&nbsp;&nbsp;Queue接口：提供几个基本方法：offer、poll、peek等，已知实现类有LinkedList、PriorityQueue等
9.TreeMap、HashMap、LinkedHashMap的区别
&nbsp;&nbsp;&nbsp;&nbsp;LinkedHashMap可以保证HashMap集合有序，存入的顺序和取出的顺序一致
&nbsp;&nbsp;&nbsp;&nbsp;TreeMap实现SortMap接口，能够把它保存的记录根据键排序，默认是键值得升序排序，也可以指定排序的比较器，当用Iterator遍历TreeMap时，得到的记录是排过序的
&nbsp;&nbsp;&nbsp;&nbsp;HashMap不保证排序，即为无序的，具有很快的访问速度。HashMap最多只允许一条记录的键为null；允许多条记录的值为null；HashMap不支持线程的同步
&nbsp;&nbsp;&nbsp;&nbsp;在开发过程中使用HashMap比较多，在Map中插入、删除和定位元素，HashMap是最好的选择
&nbsp;&nbsp;&nbsp;&nbsp;如果要按自然顺序或自定义顺序排序遍历键，那么TreeMap会更好
&nbsp;&nbsp;&nbsp;&nbsp;如果需要输出的顺序和输入的相同，那么LinkedHashMap可以实现
10.HashMap、HashTable、ConcurrentHashMap区别
&nbsp;&nbsp;&nbsp;&nbsp;HashTable:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;底层：数组+链表，无论key还是value都不能为null，线程安全，实现线程安全的方式是在修改数据时锁住整个HashTable，效率低
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;初始size为11，扩容：newSize = oldSize \* 2 + 1
&nbsp;&nbsp;&nbsp;&nbsp;HashMap:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;底层：数组+链表+红黑树，可以存储null键和null值，null键只能有一个，线程不安全
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;初始size为16，扩容：newSize = oldSize \* 2，size一定是2的n次幂
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;扩容针对整个Map，每次扩容时，原来数组中的元素依次重新计算存放位置，并重新插入
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;插入元素才判断该不该扩容，有可能无效扩容(插入后如果扩容，如果没有插入，就会产生无效扩容)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当Map中的元素总数超过Entry数组的75%，触发扩容操作，为了减少链表长度，元素分配更加均匀
&nbsp;&nbsp;&nbsp;&nbsp;ConcurrentHashMap：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;底层：分段数组+链表，线程安全
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;通过把整个Map分为N个Segment，可以提供相同的线程安全，但是效率提高N倍，默认提升16倍
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;HashTable的synchronized是针对整张Hash表，即每次锁住整张表让线程独占。ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;有些方法需要跨段，比如size()和containsValue()，他们可能需要锁定整个表而不仅仅是某个分段，这需要按顺序锁定所有段，操作完毕后，又按顺序释放所有段的锁
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;扩容：段内扩容(段内元素超过该段对应Entry数组长度的75%触发扩容，不会对整个map进行扩容)，插入前检测需不需要扩容，有效避免无效扩容
11.AOP,OOP,OOD的理解
&nbsp;&nbsp;&nbsp;&nbsp;OOD：面向对象设计(Object Oriented Design)方法是OO方法中一个中间过渡环节。其主要作用是对OOA分析的结果作进一步的规范化管理，以便能够被OOP直接接受
&nbsp;&nbsp;&nbsp;&nbsp;OOP：面向对象编程(Object Oriented Programming)是一种计算机编程架构。OOP的一条基本原则是计算机程序是由单个能够起到子程序作用的单元或对象组合而成
&nbsp;&nbsp;&nbsp;&nbsp;AOP：面向切面编程(Aspect Oriented Programming)是对业务逻辑又进行了进一步的抽取，将多种业务逻辑中的公用部分抽取出来做成一种服务(比如日志记录、性能统计、安全验证等)，从而实现代码复用。另外这种服务通过配置可以动态的给程序添加统一控制，利用AOP可以对业务逻辑的各个部分进行分离，从而使业务逻辑各部分之间的耦合度降低
12.内存溢出与内存泄漏
&nbsp;&nbsp;&nbsp;&nbsp;内存溢出：是指程序在申请内存时，没有足够的内存空间供其使用，出现OutOfMemoryError.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;产生原因:1.JVM内存过小  2.程序不严密，产生过多的垃圾
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;程序体现:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.内存中加载的数据量过于庞大，如一次从数据库取出过多数据
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.集合类中有对对象的引用，使用完后未清空，使得JVM不能回收
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.代码中存在死循环或循环产生过多重复的对象实体
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.使用的第三发软件中的BUG
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.启动参数内存值设定的过小
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;解决办法：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.增加JVM的内存大小：对于tomcat容器，找到tomcat在电脑中安装目录，进入这个目录，然后进入bin目录中，在window环境下找catalina.bat，在linux环境中找catalina.sh。编辑catalina.bat文件，找到JAVA_OPTS(具体来说是 set "JVAV_OPTS=%JAVA_OPTS% %LOGGING_MANAGER%")这个选项的位置，这个参数是Java启动的时候，需要的启动参数。也可以在操作系统的环境变量中对JAVA_OPTS进行设置，因为tomcat启动的时候，也会读取操作系统中的环境变量的值，进行加载。如果是修改操作系统的环境变量，需要重启机器，在重启tomcat，如果是修改tomcat的配置文件，需要将配置文件保存，然后重启tomcat，设置就能生效
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.优化程序，释放垃圾：主要思路就是避免程序体现的问题。避免死循环，防止一次载入太多数据，提高程序健壮性及时释放。因此，从根本上解决内存溢出的唯一方法就是修改程序，及时释放没用的对象，释放内存空间
&nbsp;&nbsp;&nbsp;&nbsp;内存泄漏：是指程序在申请内存后，无法释放已申请的内存空间，一次内存泄漏的危害可以忽略，但内存泄漏堆积后果很严重，无论多少内存，迟早会被占光
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在java中，内存泄漏就是存在一些被分配的对象，这些对象具有以下两个特点:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.这些对象是可达的，即在有向图中，存在通路与其相连
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.这些对象是无用的，即程序以后不会再使用这些对象
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果满足这两个条件，这些对象就可以判定java中的内存泄漏，这些对象不会被GC所回收，然而它却占用内存
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;解决办法:提高程序的健壮性，因为内存泄漏是纯代码层面的问题