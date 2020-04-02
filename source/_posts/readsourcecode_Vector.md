---
title: 小吴读源码记录之Vector
date: 2020-04-02 16:18:35
tags: sourcecode
---
```java
public Vector(int initialCapacity, int capacityIncrement) {
  super();
  if (initialCapacity < 0)
    throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
  this.elementData = new Object[initialCapacity];
  this.capacityIncrement = capacityIncrement;
}
```
个人理解：由此可见，Vector底层为数组，所以具有查询快，读写慢的特点。
<!-- more -->
