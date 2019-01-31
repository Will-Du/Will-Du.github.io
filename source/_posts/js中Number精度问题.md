---
title: js中Number精度问题
date: 2019-01-27 20:07:10
tags: js
---
在项目里遇到一个问题，就是利用ajax回传一个BigInteger类型的数值，将此值取出时，发现数值不对。
```js
// 在浏览器控制台上打印一个从后台传过来的BigInteger类型的数值
console.log(21824591533692098101)

// 显示如下
21824591533692097000
```
原因:
	由于JavaScript中Number类型的自身原因，并不能完全表示BigInteger型的数字，在BigInteger长度大于17位时会出现精度丢失的问题。

解决方法:
	1.后台转成String类型传到前台
	2.让前端支持Long类型
