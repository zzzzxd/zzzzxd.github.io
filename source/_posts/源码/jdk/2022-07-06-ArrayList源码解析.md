---
title: ArrayList源码解析
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-06 23:47:02
subtitle:
tags:
- java
- jdk源码
categories:
- 源码
  - jdk
---

>ArrayList几乎是最常用的一种数据结构,本篇就研究一下ArrayList的常用操作的底层实现。通过学习底层实现的细节，帮助自己写出更优秀的代码 。

### 整体结构

1. 结构图

​	ArrayList实际上就是封装了一个数组 ,代码体现如下:

```java
//封装的数组
transient  Object[] elementData;
```

![](1.jpg)

2. 类注释信息		

- 允许 put null 值，会自动扩容；
- size、isEmpty、get、set、add 等方法时间复杂度都是 O (1)；

3. 常见属性

```java
//默认数组大小10
  private static final int DEFAULT_CAPACITY = 10;
 //数组使用的大小
  private int size;
  transient  Object[] elementData;  
```

- DEFAULT_CAPACITY 表示数组的初始大小，默认是 10，这个数字要记住；
-  size 表示当前数组的大小，类型 int，没有使用 volatile 修饰，非线程安全的；
- 还有一个特别重要的变量,继承自AbstractList 

```java
 protected transient int modCount = 0;
```

​		这个变量与ArrayList源码注释中提到的"fail-fast"有关，这个变量源码中有详细的解释，翻译过来大致如下 ：

> 此列表在结构上被修改的次数。结构修改是指改变列表的大小，或以某种方式干扰列表，使正在进行的迭代可能产生不正确的结果。
>
> 该字段由迭代器和列表迭代器方法返回的迭代器和列表迭代器实现使用。如果此字段的值意外更改，迭代器（或列表迭代器）将抛出ConcurrentModificationException，以响应下一个、删除、上一个、设置或添加操作。这提供了快速失效行为，而不是在迭代过程中面对并发修改时的不确定性行为。
>
> 子类使用此字段是可选的。如果子类希望提供故障快速迭代器（和列表迭代器），那么它只需在其add（int，E）和remove（int）方法（以及它覆盖的任何其他导致列表结构修改的方法）中增加该字段。对add（int，E）或remove（int）的单个调用只能向该字段添加一个，否则迭代器（和列表迭代器）将抛出虚假的ConcurrentModificationException。如果实现不希望提供故障快速迭代器，则可以忽略此字段。

什么意思呢，所谓的"fail-fast"就是指如果在迭代时，容器发生结构性的改变，元素减少或者增多（注意更新不算）,那么遍历时会立刻抛出异常结束迭代，而这个"fail-fast"机制就是通过检查modCount变量来实现的，下文在源码中我会再详细解释。 算法导论中 也曾提过 "循环不变性",是指如果在循环的某一次迭代开始之前是正确的，那么在下一次迭代开始之前，也是正确的，感觉是有一丢丢的联系。在很多框架的实现中也见过类似的操作，对List中的元素，会先copy一份出来，再去迭代，目的就是避免迭代过程中容器发生变更而必须加锁的操作。



### 常用操作的源码解析 

1. 初始化

​		初始化代码有3种:

```java
//指定容量
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
      this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
      this.elementData = EMPTY_ELEMENTDATA;
    } else {
      throw new IllegalArgumentException("Illegal Capacity: "+
                                         initialCapacity);
    }
  }
//  无参数构造器，默认是空数组
  public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
  }
//指定初始数据初始化
  public ArrayList(Collection<? extends E> c) {
    //elementData 是保存数组的容器，默认为 null
    elementData = c.toArray();
    //如果给定的集合（c）数据有值，则进行拷贝赋值操作
    if ((size = elementData.length) != 0) {
      // c.toArray might (incorrectly) not return Object[] (see 6260652)
      //如果集合元素类型不是 Object 类型，才开始拷贝，否则不执行
      if (elementData.getClass() != Object[].class) {
        elementData = Arrays.copyOf(elementData, size, Object[].class);
      }
    } else {
      // 给定集合（c）无值，则默认空数组
      this.elementData = EMPTY_ELEMENTDATA;
    }
  }
```

注意: ArrayList 无参构造器初始化时，默认大小是空数组，并不是10，10 是在第一次 add 的时候扩容的数组值。

2. 新增与扩容

​	添加元素的逻辑很简单，首先判断是不是需要扩容，保证容量够用，然后添加元素。

```java
public boolean add(E e) {
    //确保数组大小足够，不够需要扩容
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    //直接赋值，线程不安全的
    elementData[size++] = e;
    return true;
  }
```

继续看 ensureCapacityInternal()

```java
private void ensureCapacityInternal(int minCapacity) {
    //如果是空数组，就从最小容量和默认容量10之间取最大值
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
      minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    //确保容积足够
    ensureExplicitCapacity(minCapacity);
  }
  private void ensureExplicitCapacity(int minCapacity) {
    //记录数组被修改
    modCount++;
    // 如果我们希望的最小容量大于目前数组的长度，那么就扩容
    if (minCapacity - elementData.length > 0)
      grow(minCapacity);
  }
```

最终实际是在grow方法中进行了扩容，并且做了旧值的拷贝

```java
//老的数组大小2倍，最后把现有数据拷贝到新的数组里面去
  private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    // oldCapacity >> 1 是把 oldCapacity / 2 的意思
    int newCapacity = oldCapacity + (oldCapacity >> 1);

    // 如果扩容后的值 < 我们的期望值，扩容后的值就等于我们的期望值
    if (newCapacity - minCapacity < 0)
      newCapacity = minCapacity;

    // 如果扩容后的值 > jvm 所能分配的数组的最大值，那么就去 Integer 的最大值
    if (newCapacity - MAX_ARRAY_SIZE > 0)
      newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    // 通过复制进行扩容
    elementData = Arrays.copyOf(elementData, newCapacity);
  }
```

添加以及扩容操作也比较简单，有两个细节，需要注意一下

- 扩容的规则并不是翻倍，是原来容量大小 + 容量大小的一半，直白来说，扩容后的大小是 原来容量的 1.5 倍；

- ArrayList 中的数组的最大值是 Integer.MAX_VALUE，超过这个值， JVM 就不会给数组 分配内存空间了。

3. 扩容的具体实现

​	实际上，我们看到最终调用的扩容，不过是用工具类新生成了一个数组，而这个工具类的底层实际调用的也是系统类，进行数组的拷贝

```java
Params:
  src – the source array.
  srcPos – starting position in the source array.
  dest – the destination array.
  destPos – starting position in the destination data.
  length – the number of array elements to be copied
public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
```

4. 删除

​	删除元素有两种，一种是根据下标删除，一种是根据值删除

```java
//根据数组下标去删除
  public E remove(int index) {
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
      System.arraycopy(elementData, index+1, elementData, index,
                       numMoved);
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
  }
```

```java
// 根据值去删除
  public boolean remove(Object o) {
    // 如果值是空的，找到第一个值是空的删除
    if (o == null) {
      for (int index = 0; index < size; index++)
        if (elementData[index] == null) {
          fastRemove(index);
          return true;
        }
    } else {
      // 值不为空，找到第一个和入参相等的删除
      for (int index = 0; index < size; index++)
        // 这里是根据  equals 来判断值相等的
        if (o.equals(elementData[index])) {
          fastRemove(index);
          return true;
        }
    }
    return false;
  }
```

5. 迭代器遍历

​	Iterator我们来详细解释一下，这种遍历的思想在国外的很多企业算法面试题中都有涉及，并且它还是一种设计模式，同时我们再看下上文中提到的modCount是如何来实现“fail-fast”机制的。

首先看一下ArrayList的iterator方法

```java
 //源码带注释 * <p>The returned iterator is <a href="#fail-fast"><i>fail-fast</i></a>.
public Iterator<E> iterator() {
    return new Itr();
}
```

实际就是返回来一个Iterator接口的实现`private class Itr implements Iterator<E>`

我们先看一下Iterator接口，再看一下在ArrayList中的实现

```java
public interface Iterator<E> {
    boolean hasNext();

    E next();

    default void remove() {
        throw new UnsupportedOperationException("remove");
    }
    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
}
```

接口中就这几个方法，其中`hasNext()`、`next()`、`remove()`方法通常是迭代器重点使用的方法,而`forEachRemaining()`方法我们也看到实际调用的也是迭代器，它是传入的是一个函数式接口, 接下来我们重点关注前三种方法的实现，先看下Iterator在ArrayList中的实现类

```java
private class Itr implements Iterator<E> {
  // 迭代过程中，下一个元素的位置，从 0 开始，用来控制拿下一个元素
    int cursor;       // index of next element to return
    // 新增时表示上一次迭代过程中，索引的位置，删除成功时为 -1
    int lastRet = -1; // index of last element returned; -1 if no such
    // 迭代过程中期望数组修改版本号
    int expectedModCount = modCount;
  	public boolean hasNext() {...}
  	public E next() {...}
    public void remove() {...}
  	public void forEachRemaining(Consumer<? super E> consumer){...}
    final void checkForComodification() {...}
}
```

hasNext()方法

```java
 public boolean hasNext() {
      return cursor != size;
    }
```



next()方法

```java
public E next() {
      //迭代过程中，判断版本号有无被修改，有被修改，抛 ConcurrentModificationException 异常
      checkForComodification();
      //本次迭代过程中，元素的索引位置
      int i = cursor;
      if (i >= size)
        throw new NoSuchElementException();
      Object[] elementData = ArrayList.this.elementData;
      if (i >= elementData.length)
        throw new ConcurrentModificationException();
      // 下一次迭代时，元素的位置
      cursor = i + 1;
      // 返回元素值
      return (E) elementData[lastRet = i];
    }
```

有关这个"fail-fast",我们看下checkForComodification()

```java
final void checkForComodification() {
      if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
    }
```

迭代器一创建出来，就会赋值expectedModCount，然后每次操作前会比较modCount与它的值，用来做检查，而迭代器删除的时候，会重新赋值`expectedModCount = modCount`,保证可以通过检查，下面我们也会看到。

remove()方法

```java
public void remove() {
      // 如果上一次操作时，数组的位置已经小于 0 了，说明数组已经被删除完了
      if (lastRet < 0)
        throw new IllegalStateException();
      //迭代过程中，判断版本号有无被修改，有被修改，抛 ConcurrentModificationException 异常
      checkForComodification();

      try {
        ArrayList.this.remove(lastRet);
        cursor = lastRet;
        // -1 表示元素已经被删除，这里也防止重复删除
        lastRet = -1;
        // 删除元素时 modCount 的值已经发生变化，再此赋值给 expectedModCount
        expectedModCount = modCount;
      } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
      }
    }
```

### 线程安全

​	ArrayList 有线程安全问题的本质，是因为 ArrayList 自身的 elementData、size、modConut 在进行各种操作时，都没有加锁，而且这些变量的类型并非是可见（volatile）的，所以如果多 个线程对这些变量进行操作时，可能会有值被覆盖的情况。

类注释中推荐我们使用 Collections#synchronizedList 来保证线程安全，SynchronizedList 是 通过在每个方法上面加上锁来实现，虽然实现了线程安全，但是性能大大降低，具体实现源码：

```java
public boolean add(E e) { 
  synchronized (mutex) {// synchronized 是一种 重量级锁，但具有锁升级的概念，mutex 表示一把互斥锁 
    return c.add(e); 
  }
}
```

### 总结

​	本文从 ArrayList 整体架构出发，落地到初始化、新增、扩容、删除、迭代等核心源码实现，我 们发现 ArrayList 其实就是围绕底层数组结构，各个 API 都是对数组的操作进行封装，让使用者 无需感知底层实现，只需关注如何使用即可。
