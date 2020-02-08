---
title: SQL语句中where条件后写1=1是什么意思
date: 2020-01-11 18:39:33
tags: 数据库
---
&nbsp;&nbsp;&nbsp;&nbsp;这段代码应该是由程序(例如java)中生成的，where条件中1=1之后的条件是通过if动态变化的。例如:
```java
String sql = "select * from table_name where 1=1";
if(conditon 1){
    sql = sql + " and var2 = value2";
}
if(conditon 2) {
    sql = sql + " and var3 = value3";
}
```
<!-- more -->
&nbsp;&nbsp;&nbsp;&nbsp;where 1=1是为了避免where关键字后面的第一个词直接就是“and”而导致语法错误。
&nbsp;&nbsp;&nbsp;&nbsp;where后面总要有语句，加上了1=1后就可以保证语法不会出错！
&nbsp;&nbsp;&nbsp;&nbsp;select * from table where 1=1;因为table中根本没有名称为1的字段，所以该SQL等效于select * from table;这个SQL语句很明显是全表扫描，需要大量的IO操作，数据量越大越慢，建议查询时增加必输项，即where 1=1后面追加一些常用的必选条件，并且将这些必选条件建立适当的索引，效率会大大提高。
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">拷贝表</b>
```sql
create table table_name
as
select * from source_table
where 1=1;
```
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">复制表结构</b>
```sql
create table table_name
as
select * from source_table
where 1<>1;
```
