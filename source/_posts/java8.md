---
title: java8的简单用法
date: 2019-01-13 10:44:05
tags: Java
---

## 以下为java8的一些基本用法

1.按条件过滤集合(filter)：
```java
List<Employee> list = employeeList.stream().filter(e -> e.getAge() < 30 && "A1".equal(e.getDept())).collect(Collectors.toList());
```

2.取集合中某个属性作为新的集合(map)：
```java
List<String> deptList = employeeList.stream().map(Employee::getDept).collect(Collectors.toList());
```

3.去重(distinct)：
```java
// 第一个distinct()是把相同对象先去重，第二个distinct()是把取得的值去重
List<String> deptList = employeeList.stream().distinct().map(Employee::getDept).distinct().collect(Collectors.toList());
```
<!-- more -->
4.分组(groupingBy):
```java
Map<String,List<Employee>> map = employeeList.stream(). collect(groupingBy(Employee:getDept));
```

5.循环(foreach)：
```java
// 循环赋值
employeeList.foreach(e -> {e.setDept("A1"); e.setAge(25);});
```


