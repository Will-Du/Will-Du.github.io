---
title: js截取字符串
date: 2019-03-10 11:01:34
tags: js
---

此代码为vue项目中，用来控制字符串显示问题的过滤器
```js
/*
	* 参数说明：
	* value：传入的字符串
	* maxLength: 字符串显示的最大长度
	* length：超出该值得部分用'...'
	* */
ShowStr: function(value,maxLength,length) {
	if(!value) {
		return '';
	} else if(value.length <= maxLength) {
		return value;
	} else {
	return value.substr(0,length) + '...';
	}
}
```
