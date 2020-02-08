---
title: jquery判断输入框的值所占的字节
date: 2019-01-15 16:18:13
tags: JQuery
---

### 判断输入框的值所占的字节

Oracle中字符集为UTF-8时，一个汉字占3个字节，因此有时候需要限制栏位长度，防止存储数据报异常。

```js
function getByteLen() {
	var value = $("#id").val();
	var len = 0;
	for (var i = 0; i < value.length; i++) {
		var a = value.charAt(i);
	// 中文为3个字节，其余字符占2个字节
	if (a.match(/[^\x00-\xff]/ig) != null) {
		len += 3;
	}
	else {
		len += 2;
	}
	}
	return len;
}
```
