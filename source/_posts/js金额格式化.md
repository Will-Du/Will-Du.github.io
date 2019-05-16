---
title: js金额格式化
date: 2019-03-10 11:06:29
tags: js
---

```js
/*
	* 参数说明：
	* number：要格式化的数字
	* flag : 货币符号
	* decimals：保留几位小数
	* dec_point：小数点符号
	* thousands_sep：千分位符号
	* */
formatMoney: function(number,flag,decimals,dec_point,thousands_sep) {
	if(!number) {
	  if(number == 0) {
		if(flag) {
		  return flag + ' ' + 0;
		} else {
		  return 0;
		}
	  } else {
		if(flag) {
		  return flag + ' ' + 0;
		} else {
		  return 0;
		}
	  }
	}  else {
	  if(decimals) {
		decimals = decimals > 0 && decimals <= 20 ? decimals : 2;
	  } else {
		decimals = 2;
	  }
	  
	  number = (number + '').replace(/[^0-9+-Ee.]/g, '');
	  var n = !isFinite(+number) ? 0 : +number,
		  prec = !isFinite(+decimals) ? 0 : Math.abs(decimals),
		  sep = (typeof thousands_sep === 'undefined') ? ',' : thousands_sep,
		  dec = (typeof dec_point === 'undefined') ? '.' : dec_point,
		  s = '',
		  toFixedFix = function (n, prec) {
			  var k = Math.pow(10, prec);
			  return '' + Math.ceil(n * k) / k;
		  };

	  s = (prec ? toFixedFix(n, prec) : '' + Math.round(n)).split('.');
	  var re = /(-?\d+)(\d{3})/;
	  while (re.test(s[0])) {
		  s[0] = s[0].replace(re, "$1" + sep + "$2");
	  }

	  if ((s[1] || '').length < prec) {
		  s[1] = s[1] || '';
		  s[1] += new Array(prec - s[1].length + 1).join('0');
	  }

	  var returnStr = s.join(dec);
	  if(flag) {
		returnStr = flag + " " + returnStr;
	  }
	  return returnStr;
	}
}
```