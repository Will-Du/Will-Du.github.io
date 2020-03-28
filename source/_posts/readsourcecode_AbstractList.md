---
title: 小吴读源码记录之AbstractList
date: 2020-03-28 21:57:51
tags: sourcecode
---
```java
public boolean add(E e) {
  add(size(), e);
  return true;
}

public void add(int index, E element) {
  throw new UnsupportedOperationException();
}

public E remove(int index) {
  throw new UnsupportedOperationException();
}
```
个人理解：AbstractList没有add与remove的方法，使用则会抛出异常。
<!-- more -->
```java
public void clear() {
  removeRange(0, size());
}

protected void removeRange(int fromIndex, int toIndex) {
  ListIterator<E> it = listIterator(fromIndex);
  for (int i=0, n=toIndex-fromIndex; i<n; i++) {
    it.next();
    it.remove();
  }
}

public ListIterator<E> listIterator(final int index) {
  rangeCheckForAdd(index);

  return new ListItr(index);
}

private void rangeCheckForAdd(int index) {
  if (index < 0 || index > size())
    throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}

private class ListItr extends Itr implements ListIterator<E> {
  ListItr(int index) {
    cursor = index;
  }

  public boolean hasPrevious() {
    return cursor != 0;
  }

  public E previous() {
    checkForComodification();
    try {
      int i = cursor - 1;
      E previous = get(i);
      lastRet = cursor = i;
      return previous;
    } catch (IndexOutOfBoundsException e) {
      checkForComodification();
      throw new NoSuchElementException();
    }
  }

  public int nextIndex() {
    return cursor;
  }

  public int previousIndex() {
    return cursor-1;
  }

  public void set(E e) {
    if (lastRet < 0)
      throw new IllegalStateException();
      checkForComodification();

      try {
        AbstractList.this.set(lastRet, e);
        expectedModCount = modCount;
      } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
      }
  }

  public void add(E e) {
    checkForComodification();

    try {
      int i = cursor;
      AbstractList.this.add(i, e);
      lastRet = -1;
      cursor = i + 1;
      expectedModCount = modCount;
    } catch (IndexOutOfBoundsException ex) {
      throw new ConcurrentModificationException();
    }
  }
}

private class Itr implements Iterator<E> {
  /**
   * Index of element to be returned by subsequent call to next.
   */
  int cursor = 0;

  /**
   * Index of element returned by most recent call to next or
   * previous.  Reset to -1 if this element is deleted by a call
   * to remove.
   */
  int lastRet = -1;

  /**
   * The modCount value that the iterator believes that the backing
   * List should have.  If this expectation is violated, the iterator
   * has detected concurrent modification.
   */
  int expectedModCount = modCount;

  public boolean hasNext() {
    return cursor != size();
  }

  public E next() {
    checkForComodification();
    try {
      int i = cursor;
      E next = get(i);
      lastRet = i;
      cursor = i + 1;
      return next;
    } catch (IndexOutOfBoundsException e) {
      checkForComodification();
      throw new NoSuchElementException();
    }
  }

  public void remove() {
    if (lastRet < 0)
      throw new IllegalStateException();
      checkForComodification();

      try {
        AbstractList.this.remove(lastRet);
        if (lastRet < cursor)
          cursor--;
        lastRet = -1;
        expectedModCount = modCount;
      } catch (IndexOutOfBoundsException e) {
        throw new ConcurrentModificationException();
      }
  }

  final void checkForComodification() {
    if (modCount != expectedModCount)
      throw new ConcurrentModificationException();
  }
}
```
个人理解：当AbstractList调用clear，会removeRange方法，将0和长度传入，然后调用Itr判断是否有下一个元素，然后删除该元素。
