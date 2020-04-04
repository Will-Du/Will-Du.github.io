---
title: 小吴读源码记录之AbstractMap
date: 2020-04-04 20:34:55
tags: resourcecode
---
```java
public boolean containsValue(Object value) {
  Iterator<Entry<K,V>> i = entrySet().iterator();
    if (value==null) {
      while (i.hasNext()) {
        Entry<K,V> e = i.next();
        if (e.getValue()==null)
          return true;
      }
    } else {
      while (i.hasNext()) {
        Entry<K,V> e = i.next();
          if (value.equals(e.getValue()))
            return true;
      }
    }
  return false;
}

public boolean containsKey(Object key) {
  Iterator<Map.Entry<K,V>> i = entrySet().iterator();
  if (key==null) {
    while (i.hasNext()) {
      Entry<K,V> e = i.next();
      if (e.getKey()==null)
        return true;
    }
  } else {
    while (i.hasNext()) {
      Entry<K,V> e = i.next();
      if (key.equals(e.getKey()))
        return true;
    }
  }
  return false;
}
```
个人理解：判断是否包含某个key或者某个value，都是先取得map的迭代器，然后判断入参，再相应的判断key/value是否相等。
<!-- more -->
```java
public V get(Object key) {
  Iterator<Entry<K,V>> i = entrySet().iterator();
  if (key==null) {
    while (i.hasNext()) {
      Entry<K,V> e = i.next();
      if (e.getKey()==null)
        return e.getValue();
    }
  } else {
    while (i.hasNext()) {
      Entry<K,V> e = i.next();
      if (key.equals(e.getKey()))
        return e.getValue();
    }
  }
  return null;
}
```
个人理解：取得map中key对应的值的前提就是map中含有这个key，若不含有就返回null。
