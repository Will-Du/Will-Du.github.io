---
layout: w
title: java-Calendar常用方法总结
date: 2019-01-06 21:02:19
tags: Java
---

## 取得Calendar中的年月日时分秒

```java
package com.test;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

public class CalendarTest {

	public static void main(String[] args) throws ParseException {
		
		// 实例化Calendar
		Calendar calendar = Calendar.getInstance();
		
		// 第一种：直接将date放入
		Date date = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").parse("2019-01-06 20:30:00");
		// 将实例化的时间设为2019-01-06
		calendar.setTime(date);
		
		// 第二种：year:年, month:月-1(以0开始), date:日, hourOfDay:小时, minute:分钟, second:秒数
		calendar.set(2019, 0, 6, 20, 30, 0);
		
		// 第三种 若不设置则取当前时间有关的年月日时分秒
		calendar.set(Calendar.YEAR, 2019);
		calendar.set(Calendar.MONTH, 0);
		calendar.set(Calendar.DAY_OF_MONTH, 6);
		calendar.set(Calendar.HOUR_OF_DAY, 20);
		calendar.set(Calendar.MINUTE, 30);
		calendar.set(Calendar.SECOND, 0);
		
		// 获取calendar中设定的年，若calendar为直接实例出来，默认是当前年。
		int year = calendar.get(Calendar.YEAR);
		// 获取calendar中设定的月。注：默认为0，因此需要+1才显示当前月
		int month = calendar.get(Calendar.MONTH) + 1;
		// 获取calendar中设定的日
		int day = calendar.get(Calendar.DAY_OF_MONTH);
		// 获取calendar中设定的小时
		int hour = calendar.get(Calendar.HOUR_OF_DAY);
		// 获取calendar中设定的分钟
		int minute = calendar.get(Calendar.MINUTE);
		// 获取calendar中设定的秒
		int second = calendar.get(Calendar.SECOND);
		// 获取calendar中设定的星期，注：英国国家以星期日开始，因此需要转换一下
		int weekday = calendar.get(Calendar.DAY_OF_WEEK) -1 == 0 ? 7 : calendar.get(Calendar.DAY_OF_WEEK) - 1;
		
		System.err.println("calendar中设定的时间为：" + year + "年" + month + "月" + day + "日" + hour + "时" + minute + "分" 
		+ second + "秒  星期" + weekday);
	}
}

```

程序输出结果：
```
	calendar中设定的时间为：2019年1月6日20时30分0秒  星期7
```
<!-- more -->	
## 设定Calendar中的年月日时分秒

```java
	// 第一种：直接将date放入
	Date date = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").parse("2019-01-06 20:30:00");
	// 将实例化的时间设为2019-01-06
	calendar.setTime(date);

	// 第二种：year:年, month:月-1(以0开始), date:日, hourOfDay:小时, minute:分钟, second:秒数
	calendar.set(2019, 0, 6, 20, 30, 0);

	// 第三种 若不设置则取当前时间有关的年月日时分秒
	calendar.set(Calendar.YEAR, 2019);
	calendar.set(Calendar.MONTH, 0);
	calendar.set(Calendar.DAY_OF_MONTH, 6);
	calendar.set(Calendar.HOUR_OF_DAY, 20);
	calendar.set(Calendar.MINUTE, 30);
	calendar.set(Calendar.SECOND, 0);
```
<label style="color:red">
**注：第二种与第三种设置月日时分秒若设置为负数，则时间会倒推。
如calendar.set(2019, -1, 6, 20, 30, 0);则时间为2018-12-6 20:30:00**
</label>