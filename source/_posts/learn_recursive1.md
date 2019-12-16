---
title: 递归算法-斐波那契数列
date: 2019-12-16 11:15:34
tags: 递归算法
---
&nbsp;&nbsp;&nbsp;&nbsp;斐波那契数列是这样一个数列：1、1、2、3、5、8、13、21、34……，即第一项f(1)=1,第二项f(2)=1,……，第n项为f(n)=f(n-1)+f(n-2)。求第n项的值是多少。
<!-- more -->
### 递归函数功能 ###
&nbsp;&nbsp;&nbsp;&nbsp;假设f(n)的功能是求第n项的值，代码如下：
```java
int f(int n) {

}
```
### 找出递归结束的条件 ###
&nbsp;&nbsp;&nbsp;&nbsp;显然，当n=1或者n=2，我们可以轻易知道结果f(1)=f(2)=1。所以递归结束条件可以为n <= 2.代码如下：
```java
int f(int n) {
	if(n <= 2) {
		return 1;
	}
}
```
### 找出函数的等价关系式 ###
&nbsp;&nbsp;&nbsp;&nbsp;题目已经把等价关系式给我们了，所以我们很容易知道f(n) = f(n-1) + f(n-2)。所以最终代码如下：
```java
int f(int n) {
	if(n <= 2) {
		return 1;
	}
	return f(n-1) + f(n-2);
}
```