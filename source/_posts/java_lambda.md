---
title: 浅谈lambda表达式
date: 2020-02-04 20:54:03
tags: java
---
Java8其中一个很重要的新特性就是lambda表达式，允许我们将行为传到函数中。想想看，在Java8之前我们想要将行为传入函数，仅有的选择就是匿名内部类。Java8发布以后，lambda表达式将大量替代匿名内部类的使用，简化代码的同时，更突出了原来匿名内部类中最重要的那部分包含真正逻辑的代码。尤其是对于做数据的同学来说，当习惯使用类似scala之类的函数式编程语言以后，体会将更加深刻。现在我们就来看看Java8中lambda表达式的一些常见的写法。
<!-- more -->
<b style="color: orangered">lambda体中调用方法的参数列表和返回值类型，要和函数式接口中抽象方法的参数列表和返回值类型保持一致。</b>
<b style="color: orangered">一.替代匿名内部类</b>
&nbsp;&nbsp;&nbsp;&nbsp;lambda表达式用的最多的场合就是替代匿名内部类，实现Runnable接口是匿名内部类的经典例子。lambda表达式的功能相当强大，用()->就可以代替整个匿名内部类。
```java
public class Test1 {
    public static void main(String[] args) {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("普通，线程启动");
            }
        }
        runnable.run();
        test2();
        test3();
        test4();
        test5();				
    }
    // 无参数，无返回值
    public static void test2() {
        // "->"左边只有一个小括号，表示无参数，右边是lambda体(就相当于实现了内部类里面的方法了，(即就是一个可用的接口实现类了))
        Runnable runnable = () -> System.out.println("lambda表达式方式，线程启动");
        runnable.run();				
    }
    // 有一个参数，并且无返回值
    public static void test3() {
        // 这个e就代表所实现的接口的方法的参数
        Consumer<String> consumer = e -> System.out.println("lambda表达式方式，" + e);
        consumer.accept("传入参数");
    }
    // 有两个以上的参数，有返回值，并且lambda体中有多条语句
    public ststic void test4() {
        // lambda体中有多条语句，记得要用大括号括起来
        Comparator<Integer> com = (x, y) -> {
            System.out.println("函数式接口");
            return Integer.compare(x, y);
        };
        int compare = com.compare(100, 244);
        System.out.println("有两个以上的参数，有返回值，" + compare);
    }
    // 若lambda体中只有一条语句，return和大括号都可以省略不写
    public static void test5() {
        System.out.println("若lambda体中只有一条语句，return和大括号都可以省略不写，" + Integer.compare(100, 244));
    }
}
```
```
普通，线程启动
lambda表达式方式，线程启动
lambda表达式方式，传入参数
函数式接口
有两个以上的参数，有返回值，-1
若lambda体中只有一条语句，return和大括号都可以省略不写，-1
```
<b style="color: orangered">二.Java8四大内置函数式接口</b>
&nbsp;&nbsp;&nbsp;&nbsp;如果使用lambda还要自己写一个接口的话太麻烦，所以java自己提供了一些接口：
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1.Consumer 消费性接口：void accept(T t)</b>
```java
// 有一个参数，并且无返回值
public static void test3() {
    // 这个e就代表所实现的接口的方法的参数
    Consumer<String> consumer = e -> System.out.println("lambda表达式方式，" + e);
    consumer.accept("传入参数");
}
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">Supplier 供给型接口：T get():</b>
```java
public class Test2 {
    public static void main(String[] args) {
        ArrayList<Integer> res = getNumList(10, () -> (int)(Math.random() * 100));
        System.out.println(res);
    }
    public static ArrayList<Integer> getNumList(int num, Supplier<Integer> sup) {
        ArrayList<Integer> list = new ArrayList<>();
        for(int i = 0; i < num; i++) {
            Integer e = sup.get();
            list.add(e);
        }
        return list;
    }
}
```
```
[25, 51, 44, 50, 88, 51, 88, 62, 87, 55]
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">3.Function 函数式接口：R apply(T t):</b>
```java
public class Test2 {
    public static void main(String[] args) {
        String newStr = strHander("abc", (str)->str.toUpperCase());
        System.out.println(newStr);
        newStr = strHander(" abc ", (str)->str.trim());
        System.out.println(newStr);
    }
    public static String strHander(String str, Function<String, String> fun) {
        return fun.apply(str);
    }
}
```
```
ABC
abc
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">Predicate 断言式接口：boolean test(T t):</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一些字符串数组判断长度>2的字符串:
```java
public static void main(String[] args) {
    List<String> list = Arrays.asList("hello","java","lambda","www","ok","q");
    List<String> ret = filterStr(list, (str)->str.length>2);
    System.out.println(ret);
}
public static List<String> filterStr(List<String> list, Predicate<String> pre) {
    ArrayList<String> arrayList = new ArrayList<>();
    for(String str : list) {
        if(pre.test(str)) {
            arrayList.add(str);
        }
    }
    return arrayList;
}
```
```
[hello, java, lambda, www]
```
<b style="color: orangered">三.方法引用与构造器引用</b>
&nbsp;&nbsp;&nbsp;&nbsp;要求：实现抽象方法的参数列表和返回值类型，必须与方法引用的方法的参数列表和返回值类型保持一致。
&nbsp;&nbsp;&nbsp;&nbsp;方法引用：使用操作符"::"将类与方法分隔开来。
- 对象::实例方法名
- 类::静态方法名
- 类::实例方法名
&nbsp;&nbsp;&nbsp;&nbsp;举个例子：
```java
public static void test2 {
    Comparator<Integer> comparator = (x, y) -> Integer.compare(x, y);
    Comparator<Integer> comparator1 = Integer::compare;
    int compare = comparator.compare(1, 2);
    int compare1 = comparator1.compare(1, 2);
    System.out.println("compare:" + compare);
    System.out.println("compare1:" + compare1);
}
```
```java
compare:-1
compare1:-1
```
<b style="color: orangered">四.lambda表达式的一些常见用法</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">1.使用lambda表达式对集合进行迭代</b>
```java
public class Test3 {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("java","c#","javaScript");
        for(String str : list) {
            System.out.println("before java8," + str);
        }
        list.forEach(x -> System.out.println("after java8," + x));
    }
}
```
```
before java8,java
before java8,c#
before java8,javaScript
after java8,java
after java8,c#
after java8,javaScript
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">2.用lambda表达式实现map</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;map函数可以说是函数式编程里最重要的一个方法了。map的作用是将一个对象变换为另外一个。在我们的例子中，就是通过map方法将cost增加0.05倍的大小然后输出。
```java
public class Test3 {
    public static void main(String[] args) {
        List<Double> cost = Arrays.asList(10.0, 20.0, 30.0);
        cost.stream().map(x -> x+x*0.05).forEach(x -> System.out.println(x));
    }
}
```
```
10.5
21.0
31.5
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">3.用lambda表达式实现map与reduce</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;既然提到map。又怎么不提到reduce。reduce与map一样，也是函数式编程里最重要的几个方法之一。map的作用是将一个对象变为另外一个，而reduce实现的则是将所有值合并为一个，请看：
```java
public class Test3 {
    public static void main(String[] args) {
        List<Double> cost = Arrays.asList(10.0,20.0,30.0);
        double sum = 0;
        for(double each : cost) {
            each += each * 0.05;
            sum += each;
        }
        System.out.println("before java8," + sum);
        List<Double> list = Arrays.asList(10.0,20.0,30.0);
        double sum2 = list.stream().map(x->x+x*0.05).reduce((sum1,x) -> sum1+x).get();
        System.out.println("after java8," + sum2);
    }
}
```
```
before java8,63.0
after java8,63.0
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">4.filter操作</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;filter也是我们经常使用的一个操作。在操作集合的时候，经常需要从原始的集合中过滤掉一部分元素。
```java
public class Test3 {
    public static void main(String[] args) {
        List<Double> cost = Arrays.asList(10.0,20.0,30.0,40.0);
        List<Double> filterCost = cost.stream().filter(x -> x>25.0).collect(Collertors.toList());
        filterCost.forEach(x -> System.out.println(x));
    }
}
```
```
30.0
40.0
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">5.与函数式接口Predicate配合</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;除了在语言层面支持函数式编程风格，java8也添加了一个包，叫做java.util.function。它包含了很多类，用来支持Java的函数式编程。其中一个便是Predicate，使用java.util.function.Predicate函数是接口以及lambda表达式，可以向API方法添加逻辑，用更少的代码支持更多的动态行为。Predicate接口非常适用于做过滤。
```java
public class Test3 {
    public static void filterTest(List<String> languages, Predicate<String> condition) {
        languages.stream().filter(x -> condition.test(x)).forEach(x -> System.out.println(x + " "));		
    }
    public static void main(String[] args) {
        List<String> languages = Arrays.asList("Java","Python","scala","Shell","R");
        filterTest(languages, x -> x.startWith("J"));//Java
        filterTest(languages, x -> x.endWith("a"));// java,scala
        filterTest(languages, x -> true);// Java,Python,scala,Shell,R
        filterTest(languages, x -> false);// 
        filterTest(languages, x -> x.length() > 4);// Python,scala,Shell
    }
}
```