---
title: 小吴读源码记录之HashMap
date: 2020-04-05 18:22:27
tags: resourcecode
---
```java
/**
 * The default initial capacity - MUST be a power of two.
 */
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

/**
 * The maximum capacity, used if a higher value is implicitly specified
 * by either of the constructors with arguments.
 * MUST be a power of two <= 1<<30.
 */
static final int MAXIMUM_CAPACITY = 1 << 30;

/**
 * The load factor used when none specified in constructor.
 */
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```
个人理解：由此可以看出，默认初始长度为1*2^4=16；最大容器长度为2^30。
<!-- more -->
```java
public HashMap(int initialCapacity, float loadFactor) {
  if (initialCapacity < 0)
    throw new IllegalArgumentException("Illegal initial capacity: " + initialCapacity);
  if (initialCapacity > MAXIMUM_CAPACITY)
    initialCapacity = MAXIMUM_CAPACITY;
  if (loadFactor <= 0 || Float.isNaN(loadFactor))
    throw new IllegalArgumentException("Illegal load factor: " + loadFactor);
  this.loadFactor = loadFactor;
  this.threshold = tableSizeFor(initialCapacity);
}

static final int tableSizeFor(int cap) {
  int n = cap - 1;
  n |= n >>> 1;
  n |= n >>> 2;
  n |= n >>> 4;
  n |= n >>> 8;
  n |= n >>> 16;
  return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}

public HashMap(int initialCapacity) {
  this(initialCapacity, DEFAULT_LOAD_FACTOR);
}

public HashMap() {
  this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}

public HashMap(Map<? extends K, ? extends V> m) {
  this.loadFactor = DEFAULT_LOAD_FACTOR;
  putMapEntries(m, false);
}

final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
  int s = m.size();
  if (s > 0) {
    if (table == null) { // pre-size
      float ft = ((float)s / loadFactor) + 1.0F;
      int t = ((ft < (float)MAXIMUM_CAPACITY) ? (int)ft : MAXIMUM_CAPACITY);
      if (t > threshold)
        threshold = tableSizeFor(t);
    }
    else if (s > threshold)
      resize();
    for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
        K key = e.getKey();
        V value = e.getValue();
        putVal(hash(key), key, value, false, evict);
    }
  }
}
```
