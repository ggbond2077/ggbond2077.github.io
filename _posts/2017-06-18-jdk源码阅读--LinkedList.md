---
layout: post
title: jdk源码阅读--LinkedList
categories: [技术] 
description: jdk源码阅读--LinkedList
keywords: java
---


LinkedList也是一个变长有序集合，跟[ArrayList](http://www.ctguggbond.com/java%e6%ba%90%e7%a0%81%e9%98%85%e8%af%bb-arraylist/)一样都实现了List接口，但不同的是LinkedList以链表方式实现，导致其特性与ArrayList也有所不同。 LinkedList按位置查找需要遍历链表，不如ArrayList直接定位快，在首尾增删元素比较方便，如要在指定位置增加删除修改元素，也会有遍历链表o(n)的代价。 另外这个类实现了Deque接口提供了push，pop，add，offer，poll等方法，供我们将列表用作实现堆栈、队列或双端队列。 继承关系： ![LinkedList继承关系](https://www.ggbond.cc/wp-content/uploads/2017/06/LinkedList.png)  **成员变量声明:**

```java
        //列表的大小
        transient int size = 0;
        //指向第一个结点
        transient Node<E> first;
        //指向最后一个结点
        transient Node<E> last;
```

  这两个都为Node的泛型引用，Node定义为内部类，其中有三个变量，一个指向前一个结点，一个指向后一个结点，一个保存结点值并提供一个构造方法，定义如下：

```java
     private static class Node<E> {
            E item;
            Node<E> next;
            Node<E> prev;
            Node(Node<E> prev, E element, Node<E> next) {
                this.item = element;
                this.next = next;
                this.prev = prev;
            }
        }
```

**构造方法：** **LinkedList()** 方法内部为空，构造一个空的列表。 **LinkedList(Collection<? extends E> c)** 构造一个包含指定 collection 中的元素的列表，这些元素按其 collection 的迭代器返回的顺序排列。内部实现为调用下面将要写的addAll方法（跳转） **方法实现：** 这里先看看查找指定位置结点的方法node,此方法调用较多，链表查找始终为O（n）的复杂度，这里为了稍微快一点，比较了一下index与size/2的大小,以决定从前面还是从后面开始查找。返回找到的结点的引用。

```java
         Node<E> node(int index) {
            // assert isElementIndex(index);
            if (index < (size >> 1)) {
                Node<E> x = first;
                for (int i = 0; i < index; i++)
                    x = x.next;
                return x;
            } else {
                Node<E> x = last;
                for (int i = size - 1; i > index; i--)
                    x = x.prev;
                return x;
            }
        }
    
```

  **boolean add(E e)** 将指定元素e添加到此列表的结尾 **void addLast(E e)** 将指定元素添加到此列表的结尾 这两个将元素添加到列表结尾，内部都调用了下面的linkLast方法:

```java
      void linkLast(E e) {
            final Node<E> l = last;
            final Node<E> newNode = new Node<>(l, e, null);
            last = newNode;
            if (l == null)   //l为空说明没有元素，新插入结点为第一个结点
                first = newNode;
            else
                l.next = newNode;
            size++;
            modCount++;
        }
```


  **void add(int index, E element)** 在指定的位置插入指定的元素，如果插入位置index为最后，调用上面的LinkLast方法插入到最后，否则调用下面的linkBefore插入结点到到index位置的结点之后， 调用代码：**linkBefore(element, node(index))**

```java
        void linkBefore(E e, Node<E> succ) {
            //succ 为要添加位置的原结点
            final Node<E> pred = succ.prev;
            final Node<E> newNode = new Node<>(pred, e, succ);
            succ.prev = newNode;
            if (pred == null)  //插入位置前一结点为空的，说明新插入的结点就是第一个结点
                first = newNode;
            else
                pred.next = newNode;
            size++;
            modCount++;
        }
    
```	

  ** boolean addAll(Collection<? extends E> c)** 添加指定 collection 中的所有元素到此列表的结尾，调用下面的addAll（int index, Collection<? extends E> c）实现，形参传入的index就为size **boolean addAll(int index, Collection<? extends E> c)** 将指定 collection 中的所有元素从指定位置开始插入此列表。

```java
        public boolean addAll(Collection<? extends E> c) {
            return addAll(size, c);//调用下面重载的addAll
        }
    
        public boolean addAll(int index, Collection<? extends E> c) {
            checkPositionIndex(index);  //检查index是否越界，小于0或比当前最大结点数还大
            Object[] a = c.toArray();   
            int numNew = a.length;
            if (numNew == 0)
                return false;
    
            Node<E> pred, succ;  //pre保存插入位置的前一个结点，succ保存插入位置后一个结点
            if (index == size) {
                succ = null;
                pred = last;
            } else {
                succ = node(index);   //查找位置index的结点引用，使用较多，下面单独贴代码
                pred = succ.prev;
            }
    
            //移动pre，插入集合内元素
            for (Object o : a) {
                @SuppressWarnings("unchecked") E e = (E) o;
                Node<E> newNode = new Node<>(pred, e, null);
                if (pred == null)
                    first = newNode;
                else
                    pred.next = newNode;
                pred = newNode;
            }
            //连接后面的元素
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
    

  **void addFirst(E e)** 将指定元素插入此列表的开头，调用内部linkFirst方法：

        private void linkFirst(E e) {
            final Node<E> f = first;
            final Node<E> newNode = new Node<>(null, e, f);
            first = newNode;
            if (f == null)
                last = newNode;
            else
                f.prev = newNode;
            size++;
            modCount++;//modCount 在ArrayList已经讲过，记录修改次数，在迭代时处理线程不安全问题
        }
    
```

  **void clear()** 清空列表，从此列表中移除所有元素。 虽然可以直接将firt，last置空，清除结点间的所有连接是不必要的，但是中间所有结点置空，有助于垃圾回收。 因此这里遍历链表，将所有引用变量置空，没有引用的内存将很快被回收清理。

```java
        public void clear() {
            for (Node<E> x = first; x != null; ) {
                Node<E> next = x.next;
                x.item = null;
                x.next = null;
                x.prev = null;
                x = next;
            }
            first = last = null;
            size = 0;
            modCount++;
        }
    
```

  **Object clone()** 返回此 LinkedList 的浅表副本。 （浅拷贝）

```java
        public Object clone() {
            LinkedList<E> clone = superClone();//此superClone方法内调用父类的clone方法
            //使clone过来的副本保持纯洁...将变量初始化
            // Put clone into "virgin" state
            clone.first = clone.last = null;
            clone.size = 0;
            clone.modCount = 0;
            //用现在的元素初始化此clone
            // Initialize clone with our elements
            for (Node<E> x = first; x != null; x = x.next)
                clone.add(x.item);
    
            return clone;
        }
        private LinkedList<E> superClone() {
            try {
                return (LinkedList<E>) super.clone();
            } catch (CloneNotSupportedException e) {
                throw new InternalError(e);
            }
        }
    
```

**Iterator<E> descendingIterator()** 返回以逆向顺序在此双端队列的元素上进行迭代的迭代器。 （指向最后一个结点的迭代器，并且逆序迭代） **ListIterator<E> listIterator(int index)** 返回此列表中的元素的列表迭代器（按适当顺序），从列表中指定位置开始。 ** int size()** 返回此列表的元素数，return **size**。 **下面的方法很多作用都是一样的，大都是通过调用一些私有的方法实现，方法名字不同，原理都差不多，提供这些重复不同名的方法，易于分辨，方便用来实现栈，队列等数据结构。** **E get(int index)** 返回此列表中指定位置处的元素，调用上面说过的node方法查找index处的结点，返回结点值。 **E element()** 调用getFirst方法返回列表中第一个结点的值 ** E getFirst()** 返回此列表的第一个元素。

```java
        public E getFirst() {
            final Node<E> f = first;
            if (f == null)
                throw new NoSuchElementException();
            return f.item;
        }
    
```

  **E getLast()** 返回此列表的最后一个元素，检查是否为空后，**return last.item;** **boolean contains(Object o)** 查找此列表是否包含指定元素，内部调用下面的indexOf方法查找，元素o的位置，没找到得到返回值为-1，说明不包含此元素返回 false，否则返回true **int indexOf(Object o)** 返回此列表中首次出现的指定元素的索引，如果此列表中不包含该元素，则返回 -1。

```java
         public int indexOf(Object o) {
            int index = 0;
            //如果对象为空，则查找，结点值为空的元素。
            if (o == null) {
                for (Node<E> x = first; x != null; x = x.next) {
                    if (x.item == null)
                        return index;
                    index++;
                }
            } else {
                //否则从前往后遍历链表查找指定元素index
                for (Node<E> x = first; x != null; x = x.next) {
                    if (o.equals(x.item))
                        return index;
                    index++;
                }
            }
            return -1;
        }
    
```

**int lastIndexOf(Object o)** 返回此列表中最后出现的指定元素的索引，如果此列表中不包含该元素，则返回 -1。  从后往前查找，找到就返回，找不到返回-1，与上面的indexOf实现类似，遍历时从后往前。 **boolean offer(E e)** 将指定元素添加到此列表的末尾（最后一个元素），调用add方法。 ** boolean offerFirst(E e)** 在此列表的开头插入指定的元素，调用addFirst方法。 ** boolean offerLast(E e)** 在此列表末尾插入指定的元素，调用addLast方法。 ** E peek()** 获取但不移除此列表的头（第一个元素），这里比较简单，直接返回，不用调用getFirst方法。

```java
        public E peek() {
            final Node<E> f = first;
            return (f == null) ? null : f.item;
        }
    
```

**E poll()** 获取并移除此列表的头（第一个元素），调用unlinkFirst方法，移除第一个元素。 **E removeFirst()** 移除并返回此列表的第一个元素，调用unlinkFirst，与poll作用相同。 **E remove()** 获取并移除此列表的头（第一个元素），调用removeFirst方法。

```java
        public E poll() {
            final Node<E> f = first;
            return (f == null) ? null : unlinkFirst(f);
        }
    
        private E unlinkFirst(Node<E> f) {
            // assert f == first && f != null;
            final E element = f.item;//保存首结点的值，移除后返回此值
            final Node<E> next = f.next;
            f.item = null;
            f.next = null; // help GC帮助垃圾回收
            first = next; //首结点指向第二个结点
            if (next == null)
                last = null;
            else
                next.prev = null;
            size--;
            modCount++;
            return element;
        }
    
```

**E pop()** 从此列表所表示的堆栈处弹出一个元素，同样调用上面的**removeFirst**方法...可见也会返回pop出元素的值。

```java
        public E pop() {
            return removeFirst();
        }
        public E removeFirst() {
            final Node<E> f = first;
            if (f == null)
                throw new NoSuchElementException();
            return unlinkFirst(f);
        }
    
```

** void push(E e)** 将元素推入此列表所表示的堆栈，调用**addFirst**方法。 **E remove(int index)** 移除此列表中指定位置处的元素，调用node通过index查找到结点，再调用unlink移除指定元素。

```java
       E unlink(Node<E> x) {
            // assert x != null;
            final E element = x.item;
            final Node<E> next = x.next;  //保存下一个结点
            final Node<E> prev = x.prev;  //记录上一个结点
    >        //上一个结点接上下一个结点，如果上一个结点为空，说明移除的第一个元素，则first指向下一个结点
            if (prev == null) {
                first = next;
            } else {
                prev.next = next;
                x.prev = null;
            }
            //如果下一个结点为空，说明移除的最后一个元素，则last指向上一个结点
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

  ** boolean removeFirstOccurrence(Object o)** 从此列表中移除第一次出现的指定元素，调用下面的remove（Object o）。 ** boolean remove(Object o)** 从此列表中移除首次出现的指定元素，找到元素，调用unlink方法移除。 找不到则返回false ** boolean removeLastOccurrence(Object o)** 从此列表中移除最后一次出现的指定元素（从头部到尾部遍历列表时），与removeFirstOccurrence类似，这是从后往前找。

```java
         public boolean remove(Object o) {
            if (o == null) {
                for (Node<E> x = first; x != null; x = x.next) {
                    if (x.item == null) {
                        unlink(x);
                        return true;
                    }
                }
            } else {
               //从前往后查找，与指定元素值相同的结点，调用unlink方法移除
                for (Node<E> x = first; x != null; x = x.next) {
                    if (o.equals(x.item)) {
                        unlink(x);
                        return true;
                    }
                }
            }
            return false;
        }
    
```

**E removeLast()** 移除并返回此列表的最后一个元素，调用unlinkLast。

```java
         private E unlinkLast(Node<E> l) {
            // assert l == last && l != null;
            final E element = l.item;  //保存移除结点的值
            final Node<E> prev = l.prev;  //保存前一个结点
            l.item = null;
            l.prev = null; // help GC
            last = prev;  //last指向前一个结点
            if (prev == null)
                first = null;  //前一个结点为空，说明没有元素了，first置为空
            else
                prev.next = null;
            size--;
            modCount++;
            return element;
        }
    
```

**E set(int index, E element)** 将此列表中指定位置的元素替换为指定的元素。先通过node方法查找到对应结点，再更新其值。

```java
        public E set(int index, E element) {
            checkElementIndex(index);
            Node<E> x = node(index);
            E oldVal = x.item;
            x.item = element;
            return oldVal;
        }
    
```

**Object\[\] toArray()** 返回以适当顺序（从第一个元素到最后一个元素）包含此列表中所有元素的数组，这个数组大小刚好存放所有元素,从前往后遍历链表，保存值到对象数组返回。

```java
         public Object[] toArray() {
            Object[] result = new Object[size];
            int i = 0;
            for (Node<E> x = first; x != null; x = x.next)
                result[i++] = x.item;
            return result;
        }
    
```

**<T> T\[\]  toArray(T\[\] a)** 返回以适当顺序（从第一个元素到最后一个元素）包含此列表中所有元素的数组；返回数组类型为指定的数组类型，如果指定数组长度不够，就重新申请一个size大小的相同类型数组。 否则将数组中最后一个元素的位置置空，方便求数组长度。

```java
        public <T> T[] toArray(T[] a) {
            if (a.length < size)
                a = (T[])java.lang.reflect.Array.newInstance(
                                    a.getClass().getComponentType(), size);
            int i = 0;
            Object[] result = a;
            for (Node<E> x = first; x != null; x = x.next)
                result[i++] = x.item;
    
            if (a.length > size)
                a[size] = null;
    
            return a;
        }
```
