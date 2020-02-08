---
title: isEmpty与isBlank区别
date: 2020-01-09 15:45:48
tags: Java
---
&nbsp;&nbsp;&nbsp;&nbsp;org.apache.commons.lang.StringUtils类提供了String的常用操作，最为常用的判空有如下两种isEmpty(String str)和isBlank(String str)。
<!-- more -->
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">分析</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们通过源码来分析区别：
```java
public static boolean isEmpty(String str) {
    return str == null || str.length() == 0;
}
public static boolean isNotEmpty(String str) {
    return !isEmpty(str);
}
public static boolean isBlank(String str) {
    int strLen;
    if(str != null && (strLen = str.length()) != 0) {
        for(int i = 0; i < strLen; i++) {
            if(!Character.isWhitespace(str.charAt(i))) {
                return false;
            }
        }
        return true;				
    } else {
        return true;
    }
}
public static boolean isNotBlank(String str) {
    return !isBlank(str);
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;可以看到：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.StringUtils.isEmpty(String str)判断某字符串是否为空，为空的标准是str==null或str.length()==0.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.StringUtils.isBlank(String str)判断某字符串是否为空或长度为0或由空白符(whitespace)构成.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.StringUtils.isNotEmpty(String str)等价于!isEmpty(String str).
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.StringUtils.isNotBlank(String str)等价于!isBlank(String str).
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #00FFFF">个人建议</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我自己更喜欢使用StringUtils.isBlank(String str)来执行判空操作，因为判断的条件更多更具体，特别是进行参数校验时。
