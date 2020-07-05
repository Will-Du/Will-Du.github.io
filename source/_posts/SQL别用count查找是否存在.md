---
title: SQL别用count查找是否存在
date: 2020-07-05 16:49:23
tags: 数据库
---
根据某一条件从数据库表中查询“有”与“没有”，只有两种状态，那为什么在写SQL的时候，还要SELECT COUNT(*)呢？无论是刚入道的程序员新星，还是久经沙场的程序员老白，还是一如既往的count.
<b style="color: orangered">目前多数人的写法</b>
&nbsp;&nbsp;&nbsp;&nbsp;多次review代码，发现如下现象：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;业务代码中，需要根据一个或多个条件，查询是否存在记录，不关心有多少条记录。普遍的SQL及代码写法如下:
```java
// SQL写法
SELECT COUNT(*) FROM TABLE WHERE COLCOMN1 = 'A' AND COLCOMN2 = 'B'
// java写法
int num = XXdao.countXXbyXXX(paramer);
if(num > 0) {
    // 当存在时，执行这里的代码
} else {
    // 当不存在时，执行这里的代码
}
```
&nbsp;&nbsp;&nbsp;&nbsp;是不是感觉很OK，没有什么问题。<!-- more -->
<b style="color: orangered">优化方案</b>
```java
// SQL写法
SELECT 1 FROM TABLE WHERE COLCOMN1 = 'A' AND COLCOMN2 = 'B' LIMIT 1
// java写法
Integer exist = XXdao.countXXbyXXX(paramer);
if(exist != null) {
    // 当存在时，执行这里的代码
} else {
    // 当不存在时，执行这里的代码
}
```
&nbsp;&nbsp;&nbsp;&nbsp;SQL不再使用count,而是改用LIMIT 1，让数据库查询时遇到一条就返回，不要再继续查找还有多少条了。
&nbsp;&nbsp;&nbsp;&nbsp;业务代码中直接判断是否非空即可，SQL查询速度大大提升。
<b style="color: orangered">总结</b>
&nbsp;&nbsp;&nbsp;&nbsp;根据查询条件查出来的条数越多，性能提升的越明显，在某些情况下，还可以减少联合索引的创建。