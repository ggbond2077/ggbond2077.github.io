---
layout: post
title: jdk源码阅读--ArrayList
categories: [技术] 
description: jdk源码阅读--ArrayList
keywords: java
---


**ArrayList为变长有序数组集合，有序是指按指定序列排列，并不是字典序，其在内部维护一个Object对象数组elementData（定义为： **transient Object\[\] elementData;**），可以存储任意类型数据。在容量足够的情况下，其在尾部插入较快，当容量不足时会产生O（n）的复制代价。 顺序存储按指定位置查找元素较快，但删除时会移动大量数据，比较耗时。**  **ArrayList继承关系** ![arraylist](https://www.ggbond.cc/wp-content/uploads/2017/06/arraylist.png)   可以发现其父类已经实现List接口 为何ArrayList再次实现 ？有人说这是满足开闭原则，使ArrayList的接口更纯净。这篇博客 [http://www.cnblogs.com/bluejavababy/p/4320545.html](http://www.cnblogs.com/bluejavababy/p/4320545.html) 有讲解. 如子类不再次实现调用 getInterfaces()会为空 实现接口代理会报异常。   **构造方法：** **（1）ArrayList();**

```java
        public ArrayList() {
            this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
        }
```

其中DEFAULTCAPACITY\_EMPTY\_ELEMENTDATA声明为静态成员 private static final Object\[\] DEFAULTCAPACITY\_EMPTY\_ELEMENTDATA = {}; 可见此方法构造一个空列表，默认容量为0,当调用add方法时，如果列表为空，扩大容量为10   **（2）ArrayList(Collection<? extends E> c)** 根据指定的collection构造一个列表 **（3）ArrayList(int initialCapacity)** 构造一个具有指定初始容量的空列表   **方法：** 

****增****

 

* * *

boolean add(E e)

在列表末尾插入元素

void add(int index, E element)

在指定index位置插入元素E

boolean addAll(Collection<? extends E> c)

在列表末尾顺序插入c集合中的元素

boolean addAll(int index, Collection<? extends E> c)

在指定位置开始顺序插入c集合中元素

在所有插入过程主要在add方法内部调用**ensureCapacityInternal**方法保证容量足够 源码如下：

```java

     private void ensureCapacityInternal(int minCapacity) {
            if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
                minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
            }
            /*其中常量定义：
            * private static final int DEFAULT_CAPACITY = 10;
            * private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
            *
            * minCapacity变量代表添加元素后所需最小容量
            */
            ensureExplicitCapacity(minCapacity);
        }
        private void ensureExplicitCapacity(int minCapacity) {
            modCount++;
            // overflow-conscious code
            if (minCapacity - elementData.length > 0)
                grow(minCapacity);
            /*
            *  判断如果所需最小容量大于当前容量调用grow方法增长容量 
            *  
            *  modCount++，记录修改次数；在很多线程不安全集合内部，其内容的修改都将增加这个值， 
            *  那么在迭代器初始化过程中会将这个值赋给迭代器的 expectedModCount
            *  在迭代过程中，判断 modCount 跟 expectedModCount 是否相等，
            *  如果不相等就表示已经有其他线程修改了：注意到 modCount 声明为 volatile，保证线程之间修改的可见性。
            *  所以遍历那些非线程安全的数据结构时，尽量使用迭代器
            */
        }
    
        private void grow(int minCapacity) {
            // overflow-conscious code
            int oldCapacity = elementData.length;    
            int newCapacity = oldCapacity + (oldCapacity >> 1);  
            //newCapacity指定增加后的容量，可见  新容量=1.5*旧容量
            if (newCapacity - minCapacity < 0)
                newCapacity = minCapacity;
            if (newCapacity - MAX_ARRAY_SIZE > 0)
                newCapacity = hugeCapacity(minCapacity);
            /*
            * private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
            * MAX_ARRAY_SIZE为列表最大容量 如果1.5倍增加超过了最大容量，则调用hugeCapacity重新制定新容量
            */
            elementData = Arrays.copyOf(elementData, newCapacity);
            //调用Arrays的静态方法将copyOf 内部实现大概是新创建一个newCapacity长度数组对象，将原数组内容
            //拷贝到新数组，返回新数组。这也是ArrayList在添加元素容量不足时，主要耗时操作。
        }
        private static int hugeCapacity(int minCapacity) {
            if (minCapacity < 0) // overflow
                throw new OutOfMemoryError();
            return (minCapacity > MAX_ARRAY_SIZE) ?
                Integer.MAX_VALUE :
                MAX_ARRAY_SIZE;
            //新容量为最小需要容量或者整数类最大值
        }
    
```

**删**

 

* * *

  **void clear() ** 移除此列表中的所有元素：将所有元素置为null 再将size归零

```java
      public void clear() {
        modCount++;
        // clear to let GC do its work
        for (int i = 0; i < size; i++)
          elementData[i] = null;
           size = 0;
        }
    
```
  **E remove(int index)**移除指定位置上的元素，返回移除元素的值。移除后产生元素移动操作，比较耗时  

```java
     public E remove(int index) {
            rangeCheck(index);
            //检查index是否在合法范围内，不合法抛出IndexOutOfBoundsException异常
            modCount++;
            E oldValue = elementData(index); //记录要移除的值
            int numMoved = size - index - 1; //移除位置后面部分的长度
            if (numMoved > 0)
                System.arraycopy(elementData, index+1, elementData, index,
                                 numMoved);
            //将后面部分向前移动一个单元，元素多比较耗时。
            elementData[--size] = null; // clear to let GC do its work
            return oldValue;
    
        }
    

     public E remove(int index) {
            rangeCheck(index);
            //检查index是否在合法范围内，不合法抛出IndexOutOfBoundsException异常
            modCount++;
            E oldValue = elementData(index); //记录要移除的值
            int numMoved = size - index - 1; //移除位置后面部分的长度
            if (numMoved > 0)
                System.arraycopy(elementData, index+1, elementData, index,
                                 numMoved);
            //将后面部分向前移动一个单元，元素多比较耗时。
            elementData[--size] = null; // clear to let GC do its work
            return oldValue;
    
        }
```
    

**boolean remove(Object o)** 移除此列表中首次出现的指定元素（如果存在）不存在返回false 。 方法是遍历集合数组，找到移除元素的索引值 调用void fastRemove(int index)移除元素。 fastRemove实现方法同remove 知识不用检查范围。不返回移除的值。 **protected  void removeRange(int fromIndex, int toIndex)** 移除列表中索引在 fromIndex（包括）和 toIndex（不包括）之间的所有元素。 将toIndex后面的元素复制到原数组 从fromIndex开始的位置。将后面的元素置空，更新size

```java
    protected void removeRange(int fromIndex, int toIndex) {
            modCount++;
            int numMoved = size - toIndex;
            System.arraycopy(elementData, toIndex, elementData, fromIndex,
                             numMoved);
    
            // clear to let GC do its work
            int newSize = size - (toIndex-fromIndex);
            for (int i = newSize; i < size; i++) {
                elementData[i] = null;
            }
            size = newSize;
        }
    
```

**查**  

* * *

  **Object clone()** 返回此 ArrayList 的浅拷贝副本，其中会调用父类的clone方法，关于java中clone见：[http://blog.csdn.net/zhangjg_blog/article/details/18369201](http://blog.csdn.net/zhangjg_blog/article/details/18369201)

```java
     public Object clone() {
            try {
                ArrayList<?> v = (ArrayList<?>) super.clone();
                v.elementData = Arrays.copyOf(elementData, size);
                v.modCount = 0;
                return v;
            } catch (CloneNotSupportedException e) {
                // this shouldn't happen, since we are Cloneable
                throw new InternalError(e);
            }
        }
    
```

  **boolean contains(Object o)** 判断此列表中是否包含指定的元素，有则返回 true。调用indexOf(Object o)方法遍历集合查找。 **E get(int index)** 返回此列表中指定位置上的元素 return elementData(index)即可 **int indexOf(Object o)** 返回此列表中首次出现的指定元素的索引，或如果此列表不包含元素，则返回 -1。 **boolean isEmpty()** 如果此列表中没有元素，则返回 true ：判断size是否为零。 **int lastIndexOf(Object o)** 返回此列表中最后一次出现的指定元素o的索引，或如果此列表不包含索引，则返回 -1。 从后往前查找 **int size()** 返回此列表中的元素数  return size; **<T> T\[\] toArray(T\[\] a)** 给定一个数组a，返回包含此列表中所有元素的数组，返回数组的类型是指定数组类型。

```java

    public <T> T[] toArray(T[] a) {
            if (a.length < size)
                //如果指定数组空间不足则调用copyOf重新new一个size长度数组保存元素返回
                // Make a new array of a's runtime type, but my contents:
                return (T[]) Arrays.copyOf(elementData, size, a.getClass());
               //长度足够则直接复制到指定数组返回
            System.arraycopy(elementData, 0, a, 0, size);
            if (a.length > size)
                a[size] = null;
           
            return a;
        }
    
```

  **Object\[\] toArray()** 返回一个包含此列表中所有元素的数组。 容量刚好为元素个数。

```java
        public Object[] toArray() {
            return Arrays.copyOf(elementData, size);
        }
    
     
    
```

**改**

 

* * *

**E set(int index, E element)** 用指定的元素替代此列表中指定位置上的元素。 直接elementData\[index\] = element即可，注意检查index的合法性 **void ensureCapacity(int minCapacity)** 设置此 ArrayList 实例的最小容量。一般在构造时指定，此方法较少用。增加会耗时。add方法中会调用 **void trimToSize()** 将此 ArrayList 实例的容量调整为列表的当前所含元素大小，也较少使用，调整后会导致新添加元素必定耗时 源码如下：可以发现也复制了一次数组，如果元素较多会比较耗时

```java

        public void trimToSize() {
            modCount++;
            if (size < elementData.length) {
                elementData = (size == 0)
                  ? EMPTY_ELEMENTDATA
                  : Arrays.copyOf(elementData, size);
            }
        }
```
