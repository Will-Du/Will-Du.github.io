---
title: java8_stream
date: 2019-12-18 09:19:11
tags: java
---
##### Stream是什么 #####
&nbsp;&nbsp;&nbsp;&nbsp;Stream是Java 8中添加的一个新特性，它与java.io包里面的InputStream和OutputStream是完全不同的概念。它借助于Lambda表达式，可以让你以一种声明的方式处理数据，可以极大提高Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。
<!-- more -->
##### Stream Demo #####
```java
List<String> list = Arrays.asList("a","b","c","d","e");
list.stream()
    .filter(s -> s.startsWith("1"))
    .map(String::toUpperCase)
    .sorted()
    .forEach(System.out::println);
```
##### Stream如何工作 #####
&nbsp;&nbsp;&nbsp;&nbsp;当使用一个流的时候，通常包括三个步骤：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.获取一个数据源(source)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.数据转换
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.执行操作获取想要的结果
&nbsp;&nbsp;&nbsp;&nbsp;每次转换原有Stream对象不改变，返回一个新的Stream对象(可以有多次转换)，这就允许对其操作可以像链条一样排列，变成一个管道。
&nbsp;&nbsp;&nbsp;&nbsp;在Stram中，分为两种操作：中间操作和结束操作。
&nbsp;&nbsp;&nbsp;&nbsp;中间操作返回Stream，结束操作返回void或者非Stream结果，在demo中，filter、map、sorted都算中间操作，而forEach是一个结束操作。
##### Stream如何生成 #####
&nbsp;&nbsp;&nbsp;&nbsp;创建Stream的方式很多，最常见的是从Collections,List和Set中生成
```java
List<String> list = Arrays.asList("a1","a2","b1","c2","c1");
Stream<String> stream = list.stream();
```
&nbsp;&nbsp;&nbsp;&nbsp;在对象list上调用方法stream()返回一个常规对象Stream。
&nbsp;&nbsp;&nbsp;&nbsp;也可以从一堆已知对象中生成。
```java
Stream<String> stream = Stream.of("a1","a2","a3");
```
&nbsp;&nbsp;&nbsp;&nbsp;当然还有其他方式：
- Collection.stream()
- Collection.parallelStream()
- BufferedReader.lines()
- Files.walk()
- BitSet.stream()
- Random.ints()
- JarFile.stream()
- ......
### 常规操作 ###
##### forEach #####
&nbsp;&nbsp;&nbsp;&nbsp;forEach方法接收一个Lambda表达式，用来迭代流中的每个数据
```java
Stream.of(1,2,3).forEach(System.out::println);
// 1
// 2
// 3
```
##### map #####
&nbsp;&nbsp;&nbsp;&nbsp;map用于映射每个元素到对应的结果
```java
Stream.of(1,2,3).map(i -> i*i).forEach(System.out::println);
// 1
// 4
// 9
```
##### filter #####
&nbsp;&nbsp;&nbsp;&nbsp;filter用于通过设置的条件过滤出元素
```java
Stream.of(1,2,3).filter(i -> i == 1).forEach(System.out::println);
// 1
```
##### limit #####
&nbsp;&nbsp;&nbsp;&nbsp;limit用于获取指定数量的流
```java
Stream.of(1,2,3,4,5).limit(2).forEach(System.out::println);
// 1
// 2
```
##### sorted #####
&nbsp;&nbsp;&nbsp;&nbsp;sorted用于对流进行排序
```java
Stream.of(4,1,5).sorted().forEach(System.out::println);
// 1
// 4
// 5
```
##### Match #####
&nbsp;&nbsp;&nbsp;&nbsp;有三个match方法，从语义上说：
- allMatch:Stream中全部元素符合传入的predicate，返回true
- anyMatch:Stream中只要有一个元素符合传入的predicate，返回true
- noneMatch:Stream中没有一个元素符合传入的predicate，返回true
&nbsp;&nbsp;&nbsp;&nbsp;它们都不是要遍历全部元素才能返回结果。例如allMatch只要一个元素不满足条件，就skip剩下所有的元素，返回false。
```java
boolean result = Stream.of("a1","a2","a3").allMatch(i -> i.startsWith("a"));
System.out.println(result);
// true
```
##### reduce #####
&nbsp;&nbsp;&nbsp;&nbsp;reduce方法根据指定的函数将元素序列累积到某个值。此方法有两个参数：
- 起始值
- 累加器函数
&nbsp;&nbsp;&nbsp;&nbsp;如有有个List，希望得到所有这些元素和一些初始值的总和。
```java
int result = Stream.of(1,2,3).reduce(20,(a,b) -> a + b);
System.out.println(result);
// 26
```
##### collect #####
&nbsp;&nbsp;&nbsp;&nbsp;Collectors类提供了功能丰富的工具方法：
- toList
- toSet
- toCollection
- toMap
- .....
&nbsp;&nbsp;&nbsp;&nbsp;而这些方法，都需要通过collect方法传入
```java
Set<Integer> result = Stream.of(1,1,2,3).collect(Collectors.toSet));
System.out.println(result);
// [1,2,3]
```
&nbsp;&nbsp;&nbsp;&nbsp;collect可以把Stream数据流转化为Collection对象
### 骚技巧 ###
##### for循环 #####
&nbsp;&nbsp;&nbsp;&nbsp;除了常规的对象Stream，还有一些有特殊类型的Stream，用于处理基本数据类型int、long和doubble，它是IntStream、LongStream和DoubbleStream。
&nbsp;&nbsp;&nbsp;&nbsp;比如可以使用IntStream.range()来代替常规的for循环。
```java
IntStream.rang(1,4).forEach(System.out::println);
```
##### 随机数 #####
&nbsp;&nbsp;&nbsp;&nbsp;Random的ints方法可以返回一个随机数据流，比如返回1到100的10个随机数。
```java
Random random = new Random();
random.ints(1,100).limit(10).forEach(System.out::println);
```
##### 大小写转化 #####
```java
List<String> output = wordList.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```
### Stream特点 ###
&nbsp;&nbsp;&nbsp;&nbsp;Stream的特征可以归纳为：
- 无存储：Stream并不是一种数据结构，它只是某种数据源的一个视图。
- 安全性：对Stream的任何修改都不会修改背后的数据源，比如对stream执行过滤操作并不会删除被过滤元素，而是产生一个不包含被过滤元素的新Stream。
- 惰式执行：Stream上的操作并不会立即执行，只有等到用户真正需要结果的时候才会执行。
- 一次性：Stream只能被“消费”一次，一旦遍历过就会失效，就像容器的迭代器那样，想要再次遍历必须重新生成。
- lambda：所有Steam的操作必须以lambda表达式为参数。