---
title: 小吴读源码记录之AbstractCollection
date: 2020-03-26 19:50:56
tags: sourcecode
---
```java
public boolean contains(Object o) {
  Iterator<E> it = iterator();
  if (o==null) {
    while (it.hasNext())
      if (it.next()==null)
        return true;
  } else {
    while (it.hasNext())
      if (o.equals(it.next()))
        return true;
  }
  return false;
}
```
个人理解：如果传进来的参数为null，会通过迭代该集合，如果有null的则返回true。如果传进来的参数不为null，则还会迭代该集合，通过equals比较，如果相等则返回true。
<!-- more -->
```java
@NotNull
public Object[] toArray() {
  // Estimate size of array; be prepared to see more or fewer elements
  Object[] r = new Object[size()];
  Iterator<E> it = iterator();
  for (int i = 0; i < r.length; i++) {
    if (! it.hasNext()) // fewer elements than expected
      return Arrays.copyOf(r, i);
    r[i] = it.next();
  }
  return it.hasNext() ? finishToArray(r, it) : r;
}

public static <T> T[] copyOf(@NotNull T[] original, int newLength) {
  return (T[]) copyOf(original, newLength, original.getClass());
}

@Native public static final int   MAX_VALUE = 0x7fffffff;
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

private static <T> T[] finishToArray(T[] r, Iterator<?> it) {
  int i = r.length;
  while (it.hasNext()) {
    int cap = r.length;
    if (i == cap) {
      int newCap = cap + (cap >> 1) + 1;
      // overflow-conscious code
      if (newCap - MAX_ARRAY_SIZE > 0)
        newCap = hugeCapacity(cap + 1);
      r = Arrays.copyOf(r, newCap);
    }
    r[i++] = (T)it.next();
  }
  // trim if overallocated
  return (i == r.length) ? r : Arrays.copyOf(r, i);
}

private static int hugeCapacity(int minCapacity) {
  if (minCapacity < 0) // overflow
    throw new OutOfMemoryError("Required array size too large");
  return (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE : MAX_ARRAY_SIZE;
}
```
个人理解：先创建一个长度为默认的数组，然后获取集合，循环数组，如果集合的长度>数组的长度，则会调用finishToArray这个方法，如果集合长度<=数组长度，则会进行完全复制完成。int newCap = cap + (cap >> 1) + 1;数组扩容时2n+1，如果超出超出数组的最大值，则会取Integer的最大值最为长度。
```java
public <T> T[] toArray(@NotNullT[] a) {
  // Estimate size of array; be prepared to see more or fewer elements
  int size = size();
  T[] r = a.length >= size ? a : (T[])java.lang.reflect.Array.newInstance(a.getClass().getComponentType(), size);
  Iterator<E> it = iterator();

  for (int i = 0; i < r.length; i++) {
    if (! it.hasNext()) { // fewer elements than expected
      if (a == r) {
        r[i] = null; // null-terminate
      } else if (a.length < i) {
        return Arrays.copyOf(r, i);
      } else {
        System.arraycopy(r, 0, a, 0, i);
        if (a.length > i) {
          a[i] = null;
        }
      }
      return a;
    }
  r[i] = (T)it.next();
  }
  // more elements than expected
  return it.hasNext() ? finishToArray(r, it) : r;
}
```
```java
public boolean remove(Object o) {
  Iterator<E> it = iterator();
  if (o==null) {
    while (it.hasNext()) {
      if (it.next()==null) {
        it.remove();
        return true;
      }
    }
  } else {
    while (it.hasNext()) {
      if (o.equals(it.next())) {
        it.remove();
        return true;
      }
    }
  }
  return false;
}
```
个人理解：由此代码可见，如果该集合内有多个重复的要删除元素，remove只会删除第一个匹配到的元素。