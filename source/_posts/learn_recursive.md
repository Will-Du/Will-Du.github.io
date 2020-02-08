---
title: 递归算法-如何学习递归
date: 2019-12-15 16:38:08
tags: 算法
---
## 递归的三大要素 ##
#### 第一要素：明确你这个函数想干什么 ####
&nbsp;&nbsp;&nbsp;&nbsp;对于递归，很重要的一件事就是，<b style="color:orangered">这个函数的功能是什么，</b>它要完成什么样的一件事，而这个，是完全由你自己来定义的。也就是说，我们先不管函数里面的代码什么，而是要先明白你这个函数要用来干什么。
<!-- more -->
&nbsp;&nbsp;&nbsp;&nbsp;例如，我定了一个函数
```java
// 算n的阶乘(假设n不为0)
int f(int n){

}
```
&nbsp;&nbsp;&nbsp;&nbsp;这个函数的功能是算n的阶乘。好了，我们已经定义了一个函数，并且定义了它的功能是什么，接下来我们看第二要素。
#### 第二要素：寻找递归结束条件 ###
&nbsp;&nbsp;&nbsp;&nbsp;所谓递归，就是会在函数内部代码中，调用这个函数本身，所以，我们必须要找出<b style="color:orangered">递归的结束条件</b>，不然的话，会一直调用自己，进入无底洞。也就是说，我们需要找出<b style="color:orangered">当参数为什么值时，递归结束，之后直接把结果返回</b>，请注意，这个时候我们必须能根据这个参数的值，能够<b style="color:orangered">直接</b>知道函数的结果是什么。
&nbsp;&nbsp;&nbsp;&nbsp;例如，上面那个例子，当n=1时，那你应该能够直接知道f(n)=1。完善我们函数内部的代码，把第二要素加进代码里面，如下
```java
// 算n的阶乘(假设n不为0)
int f(int n) {
	if(n == 1) {
		return 1;
	}
}
```
&nbsp;&nbsp;&nbsp;&nbsp;有人可能会说，当n=2时，那我们可以直接知道f(n)等于多少啊，那我可以把n=2作为递归的结束条件吗？
&nbsp;&nbsp;&nbsp;&nbsp;当然可以，只要你觉得参数是什么时，你能够直接知道函数的结果，那么你就可以把这个参数作为结束的条件，所以下面这段代码也是可以的：
```java
// 算n的阶乘(假设n>=2)
int f(int n) {
	if(n == 2) {
		return 2;
	}
}
```
&nbsp;&nbsp;&nbsp;&nbsp;注意我代码里面写的注释，假设n>=2，因为如果n=1时，会被漏掉，当n<=2时，f(n)=n，所以为了更加严谨，我们可以写成这样：
```java
// 算n的阶乘(假设n不为0)
int f(int n) {
	if( n<= 2) {
		return n;
	}
}
```
#### 第三要素：找出函数的等价关系式 ####
&nbsp;&nbsp;&nbsp;&nbsp;第三要素就是，我们要<b style="color:orangered">不断缩小参数的范围</b>，缩小之后，我们可以通过一些辅助的变量或者操作，使原函数的结果不变。
&nbsp;&nbsp;&nbsp;&nbsp;例如，f(n)这个范围比较大，我们可以让f(n)=n \* f(n-1)。这样，范围就由n变成n-1了，范围变小了，并且为了原函数f(n)不变，我们需要让f(n-1)乘以n。说白了，就是要找到原函数的一个等价关系式，f(n)的等价关系式为n \* f(n-1),即f(n)=n \* f(n-1)。
&nbsp;&nbsp;&nbsp;&nbsp;找出了这个等价，继续完善我们的代码，我们把这个等价写进函数里，如下：
```java
// 算n的阶乘(假设n不为0)
int f(int n) {
	if(n <= 2) {
		return n;
	}
	// 把f(n)的等价操作写进去
	return f(n-1) * n;
}
```
&nbsp;&nbsp;&nbsp;&nbsp;至此，递归三要素已经都写进代码里了，所以这个f(n)功能的内部代码我们已经写好了。这就是递归最重要的三要素，每次做递归的时候，你就强迫自己试着去寻找这三个要素。
### 案例1.斐波那契数列 ###
&nbsp;&nbsp;&nbsp;&nbsp;斐波那契数列是这样一个数列：1、1、2、3、5、8、13、21、34……，即第一项f(1)=1,第二项f(2)=1,……，第n项为f(n)=f(n-1)+f(n-2)。求第n项的值是多少。
##### 递归函数功能 #####
&nbsp;&nbsp;&nbsp;&nbsp;假设f(n)的功能是求第n项的值，代码如下：
```java
int f(int n) {

}
```
##### 找出递归结束的条件 #####
&nbsp;&nbsp;&nbsp;&nbsp;显然，当n=1或者n=2，我们可以轻易知道结果f(1)=f(2)=1。所以递归结束条件可以为n <= 2.代码如下：
```java
int f(int n) {
	if(n <= 2) {
		return 1;
	}
}
```
##### 找出函数的等价关系式 #####
&nbsp;&nbsp;&nbsp;&nbsp;题目已经把等价关系式给我们了，所以我们很容易知道f(n) = f(n-1) + f(n-2)。所以最终代码如下：
```java
int f(int n) {
	if(n <= 2) {
		return 1;
	}
	return f(n-1) + f(n-2);
}
```
### 案例2.小青蛙跳台阶 ###
&nbsp;&nbsp;&nbsp;&nbsp;一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级台阶总共有多少种跳法。
##### 递归函数功能 #####
&nbsp;&nbsp;&nbsp;&nbsp;假设f(n)的功能是求青蛙跳上一个n级台阶总共有多少种方法，代码如下：
```java
int f(int n) {
	
}
```
##### 找出递归结束的条件 #####
&nbsp;&nbsp;&nbsp;&nbsp;直接把n压缩到很小很小，因为n越小，我们就越容易直观算出f(n)等于多少，所以当n=1时，知道f(1)=1、所以代码如下：
```java
int f(int n) {
	if(n == 1) {
		return 1;
	}
}
```
##### 找出函数的等价关系式 #####
&nbsp;&nbsp;&nbsp;&nbsp;每次跳的时候，小青蛙可以跳一个台阶，也可以跳两个台阶，也就是说，每次跳的时候，小青蛙有两种跳法。
&nbsp;&nbsp;&nbsp;&nbsp;第一种跳法：第一次跳了一个台阶，那么还剩下n-1个台阶没跳，剩下的n-1个台阶的跳法有f(n-1)种。
&nbsp;&nbsp;&nbsp;&nbsp;第二种跳法：第一次跳了两个台阶，那么还剩下n-2个台阶没跳，剩下的n-2个台阶的跳法有f(n-2)种。
&nbsp;&nbsp;&nbsp;&nbsp;所以，小青蛙的全部跳法就是这两种跳法之和了，即f(n) = f(n-1) + f(n-2)。至此，等价关系式就求出来了，代码如下：
```java
int f(int n) {
	if(n == 1) {
		return 1;
	}
	return f(n-1) + f(n-2);
}
```
&nbsp;&nbsp;&nbsp;&nbsp;上面的代码对吗？答案是不大对，当n=2时，显然会有f(2) = f(1) + f(0).我们知道，f(0)=0,按道理是递归结束，不用继续往下调用的，但我们上面的代码逻辑中，会继续调用f(0)=f(-1)+f(-2)。这会导致无限调用，进入死循环。
&nbsp;&nbsp;&nbsp;&nbsp;这也是要注意的，关于<b style="color:orangered">递归结束条件是否够严谨问题</b>，有很多人使用递归的时候，由于结束条件不够严谨，导致出现死循环。也就是说，当我们在第二步找出了一个递归结束条件的时候，可以把结束条件写进代码，然后进行第三步，但是请注意，当我们第三步找出等价函数之后，记得再返回第二步，根据第三步函数的调用关系，会不会出现一些漏掉的结束条件。就像上面，f(n-2)这个函数的调用，有可能出现f(0)的情况，导致死循环，所以我们把它补上。代码如下：
```java
int f(int n) {
	if(n <= 1) {
		return n;
	}
	return f(n-1) + f(n-2);
}
```
### 案例3.反转单链表 ###
&nbsp;&nbsp;&nbsp;&nbsp;例如链表为：1->2->3->4，反转后为4->3->2->1
```java
class Node {
	int data;
	Node next;
}
```
##### 定义递归函数功能 #####
&nbsp;&nbsp;&nbsp;&nbsp;假设函数reverseList(head)的功能是反转单链表，其中head表示链表的头结点。代码如下：
```java
Node reverseList(Node head) {

}
```
##### 寻找结束条件 #####
&nbsp;&nbsp;&nbsp;&nbsp;当链表只有一个结点，或者如果是空表的话，你应该知道结果吧，直接啥也不用干，直接把head返回。代码如下：
```java
Node reverseList(Node head) {
	if(head == null || head.next == null) {
		return head;
	}
}
```
##### 寻找等价关系 #####
&nbsp;&nbsp;&nbsp;&nbsp;这个的等价关系不像n是个数值那样，比较容易寻找。但是它的等价条件中，一定是范围不断缩小，对于链表来说，就是链表的结点个数不断在变小，所以，如果你实在找不出，你就先对reverseList(head.next)递归走一遍，看看结果是咋样的。例如链表结点为head->1->2->3->4，我们就缩小范围，先对2->3->4递归试试，代码如下：
```java
Node reverseList(Node head) {
	if(head == null || head.next == null) {
		return head;
	}
	Node newList = reverseList(head.next);
}
```
&nbsp;&nbsp;&nbsp;&nbsp;我们在第一步时，就已经定义了reverseList函数的功能可以把一个单链表反转，所以，我们对2->3->4反转之后的结果应该是这样：head->1->2<-3<-4<-newList。我们把2->3->4递归成4->3->2。不过1这个结点我们并没有去碰它，所以1的next结点仍然是连接2。其实，接下来就简单了，我们接下来只需<b style="color:orangered">把结点2的next指向1，然后把1的next指向null，不就行了？</b>即通过改变newList链表之后的结果：newList->4->3->2->1->null，也就是说，reverseList(head)等价于reverseList(head.next)+<b style="color:orangered">改变一下1,2这两个结点的指向</b>。好了，等价关系找出来了，代码如下：
```java
// 用递归的方法反转链表
public static Node reverseList(Node head) {
	// 1.递归结束条件
	if(head == null || head.next == null) {
		return head;
	}
	// 递归反转子链表
	Node newList = reverseList(head.next);
	// 改变1,2结点的指向。通过head.next获取结点2
	Node t1 = head.next;
	// 让2的next指向2
	t1.next = head;
	// 1的next指向null
	head.next = null;
	// 把调整之后的链表返回
	return newList;
	
}
```
### 递归的一些优化思路 ###
##### 1.是否考虑重复计算 #####
&nbsp;&nbsp;&nbsp;&nbsp;如果你使用递归的时候不进行优化，是有非常非常非常多的子问题被重复计算的。f(n-1)、f(n-2)……就是f(n)的子问题。
&nbsp;&nbsp;&nbsp;&nbsp;例如对于案例2那道题算f(8)，递归计算时，重复计算了两次f(5)，五次f(4)……这是非常恐怖的，n越大，重复计算的就越多，所以我们必须进行优化。
&nbsp;&nbsp;&nbsp;&nbsp;如何优化？一般我们可以把我们计算的结果保存起来，例如把f(4)的计算结果保存起来，当再次要计算f(4)的时候，我们先判断一下，之前是否计算过，如果计算过，直接把f(4)得结果取出来就可以了，没有计算过的话，再递归计算。
&nbsp;&nbsp;&nbsp;&nbsp;用什么保存呢？我们可以用数组或者HashMap保存，我们用数组来保存，把n作为我们的数组下标，f(n)作为值，例如arr[n]=f(n)。f(n)还没有计算的时候，我们让arr[n]等于一个特殊值，例如arr[n]=-1。当我们要判断的时候，如果arr[n]=-1，则证明f(n)没有计算过，否则，f(n)就已经计算过，且f(n)=arr[n]。直接把值取出来就行了。代码如下：
```java
// 我们实现假定arr数组已经初始化好了
int f(int n) {
	if(n <= 1) {
		return n;
	}
	// 先判断有没有计算过
	if(arr[n] != -1) {
		// 计算过，直接返回
		return arr[n];
	} else {
		// 没有计算过，递归计算，并且把结果保存到arr数组里
		arr[n] = f(n-1) + f(n-2);
		return arr[n];
	}
}
```
&nbsp;&nbsp;&nbsp;&nbsp;也就是说，使用递归的时候，必须要考虑有没有重复计算，如果重复计算，一定要把计算过的状态保存起来。
##### 2.考虑是否可以自底而上 #####
&nbsp;&nbsp;&nbsp;&nbsp;对于递归的问题，我们一般都是<b style="color:orangered">从上往下递归</b>的，直到递归到最底，再一层一层着把值返回。不过，有时候当n比较大的时候，例如当n=10000时，那么必须要往下递归10000层直到n<=1才将结果慢慢返回，如果n太大的话，可能栈空间会不够用。对于这种情况，其实我们是可以考虑自底向上的做法。例如我知道f(1)=1,f(2)=2;那么我们就可以推出f(3)=f(2)+f(1)=3。从而可以推出f(4),f(5)等直到f(n)。因此，我们考虑使用自底向上的方法来取代递归，代码如下：
```java
public int f(int n) {
	if(n <= 2) {
		return n;
	}
	int f1 = 1;
	int f2 = 2;
	int sum = 0;
	for(int i = 3; i <= n; i++) {
		sum = f1 + f2;
		f1 = f2;
		f2 = sum;
	}
	return sum;
}
```
&nbsp;&nbsp;&nbsp;&nbsp;这种方法，其实也被称之为递推。