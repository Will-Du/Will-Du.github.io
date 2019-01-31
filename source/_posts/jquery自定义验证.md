---
title: jquery自定义验证
date: 2019-01-15 15:52:41
tags: jquery
---

### jquery添加自定义验证

首先写个自定义验证
```js
// 当return false则出现提示信息
$.validator.addMethod("checkNumber", function(value, element) {
	
	// 若无输入，不验证
	if(value == ""){
		return true;
	}
	
	// 验证规则
	var v_regex=/^[0-9]+.?[0-9]*$/;

	if(value){
		if(!v_regex.test(value)){
	
		  return false;
		}else{
		  return true;
		}
	}else{
		return null;
	}

}, "金額需為數字");
```
<!-- more -->	
validate验证中添加该验证

```js
var ok = $("#qForm").validate({
	rules: {
		"money": {
			checkNumber: true
		}
	},
	messages: {
	
	},
	ignore: [],
	errorPlacement: function(error, element) {
		qcCommon.errorPlacement(error, element);
	},
	// 加入此段程式碼會自動focus錯誤地方
	invalidHandler: function(event, validator) {
		qcCommon.invalidHandler(event, validator);
	}
}).form();

if(!ok) {
	return;
}
```