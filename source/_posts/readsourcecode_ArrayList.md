---
title: 小吴读源码记录之ArrayList
date: 2020-03-31 15:49:06
tags: sourcecode
---
```java
public ArrayList(int initialCapacity) {
  if (initialCapacity > 0) {
    this.elementData = new Object[initialCapacity];
  } else if (initialCapacity == 0) {
    this.elementData = EMPTY_ELEMENTDATA;
  } else {
    throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
  }
}
```
个人理解：1.当入参大于0时，则声明一个长度为入参的集合；当入参等于0时，则声明一个空数组{}；当入参小于0，则抛出异常。
2.由此可以看出ArrayList的底层是数组，所以具有查询快，增删慢的特点。
<!-- more -->
```java
private void ensureCapacityInternal(int minCapacity) {
  ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

private void ensureExplicitCapacity(int minCapacity) {
  modCount++;

  // overflow-conscious code
  if (minCapacity - elementData.length > 0)
    grow(minCapacity);
}

private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

private void grow(int minCapacity) {
  // overflow-conscious code
  int oldCapacity = elementData.length;
  int newCapacity = oldCapacity + (oldCapacity >> 1);
  if (newCapacity - minCapacity < 0)
    newCapacity = minCapacity;
  if (newCapacity - MAX_ARRAY_SIZE > 0)
    newCapacity = hugeCapacity(minCapacity);
  // minCapacity is usually close to size, so this is a win:
  elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
  if (minCapacity < 0) // overflow
    throw new OutOfMemoryError();
  return (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE : MAX_ARRAY_SIZE;
}
```
个人理解：新增时会先验证集合长度是否够用，不够的话会根据自动扩容。
```java
public E remove(int index) {
  rangeCheck(index);

  modCount++;
  E oldValue = elementData(index);

  int numMoved = size - index - 1;
  if (numMoved > 0)
  System.arraycopy(elementData, index+1, elementData, index, numMoved);
  elementData[--size] = null; // clear to let GC do its work

  return oldValue;
}
```
个人理解：System.arraycopy，第一个elementData为sourceData，index+1为取sourceData的下标，第二个elementData为destData，index为将替换destData[index]元素的下标，numMoved为取sourceData[index]与之后de共numMoved元素。最后将取出元素替换到destData里。
```java
public boolean remove(Object o) {
  if (o == null) {
    for (int index = 0; index < size; index++)
      if (elementData[index] == null) {
        fastRemove(index);
        return true;
      }
  } else {
    for (int index = 0; index < size; index++)
      if (o.equals(elementData[index])) {
        fastRemove(index);
        return true;
      }
  }
  return false;
}

private void fastRemove(int index) {
  modCount++;
  int numMoved = size - index - 1;
  if (numMoved > 0)
    System.arraycopy(elementData, index+1, elementData, index, numMoved);
  elementData[--size] = null; // clear to let GC do its work
}
```
个人理解：如果删除元素，会循环匹配该元素，如果匹配到，直接删除第一个后直接返回结果。若是迭代中使用remove则会抛出异常。
```java
public void forEach(Consumer<? super E> action) {
  Objects.requireNonNull(action);
  final int expectedModCount = modCount;
  @SuppressWarnings("unchecked")
  final E[] elementData = (E[]) this.elementData;
  final int size = this.size;
  for (int i=0; modCount == expectedModCount && i < size; i++) {
    action.accept(elementData[i]);
  }
  if (modCount != expectedModCount) {
    throw new ConcurrentModificationException();
  }
}
```
个人理解：在迭代中，元素的个数是不能进行修改的，即不能add跟remove。