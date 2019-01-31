---
title: js中两个集合求并集，交集，差集
date: 2019-01-24 17:26:16
tags: js
---

现有两个数组a = [1, 2, 3]，b = [2, 4, 5]，求a，b数组的并集，交集和差集。代码如下
	
```js
// 并集
let union = a.concat(b.filter(v => !a.includes(v))) // [1,2,3,4,5]
// 交集
let intersection = a.filter(v => b.includes(v)) // [2]
// 差集
let difference = a.concat(b).filter(v => a.includes(v) && !b.includes(v)) // [1,3]
```
