---
title: 小吴读源码记录之LinkedList
date: 2020-04-02 16:10:41
tags: sourcecode
---
```java
public LinkedList(Collection<? extends E> c) {
  this();
  addAll(c);
}

public LinkedList() {
}

public boolean addAll(Collection<? extends E> c) {
  return addAll(size, c);
}

public boolean addAll(int index, Collection<? extends E> c) {
  checkPositionIndex(index);

  Object[] a = c.toArray();
  int numNew = a.length;
  if (numNew == 0)
    return false;

  Node<E> pred, succ;
  if (index == size) {
    succ = null;
    pred = last;
  } else {
    succ = node(index);
    pred = succ.prev;
  }

  for (Object o : a) {
    @SuppressWarnings("unchecked") 
		E e = (E) o;
    Node<E> newNode = new Node<>(pred, e, null);
    if (pred == null)
      first = newNode;
    else
      pred.next = newNode;
    pred = newNode;
  }

  if (succ == null) {
    last = pred;
  } else {
    pred.next = succ;
    succ.prev = pred;
  }

  size += numNew;
  modCount++;
  return true;
}

private void checkPositionIndex(int index) {
  if (!isPositionIndex(index))
    throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```
个人理解：由此可见LinkedList底层为链表，所以具有查询慢，读写快的特点。
<!-- more -->
