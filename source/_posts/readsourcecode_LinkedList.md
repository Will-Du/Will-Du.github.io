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
```java
E unlink(Node<E> x) {
  // assert x != null;
  final E element = x.item;
  final Node<E> next = x.next;
  final Node<E> prev = x.prev;

  if (prev == null) {
    first = next;
  } else {
    prev.next = next;
    x.prev = null;
  }

  if (next == null) {
    last = prev;
  } else {
    next.prev = prev;
    x.next = null;
  }

  x.item = null;
  size--;
  modCount++;
  return element;
}
```
个人理解：链表解除元素，首先判断该元素是否有前结点，若没有则说明该元素为头结点指向的元素，这时候让头结点指向该元素的后继元素；不然则让前一个元素的尾结点指向该元素的下一个节点，让该元素的前结点清空。如果该元素的尾结点指向null，说明该元素是该链表的最后一个元素，则让链表的尾结点指向该元素的前继元素；不然则让后一个元素的头结点指向该元素的前继元素，该结点的尾结点清空。最后在清空该元素。