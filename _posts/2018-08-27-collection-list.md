---
layout:       post
title:        "Java集合类List详解"
subtitle:     "剖析Java集合类List特性"
date:         2018-08-27 22:42:05
author:       "Hyuga"
header-img:   "img/2018-08-27/head-top.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - god road
---

## 集合简介
Java集合主要分为三种类型：
- list（列表）
- set（集）
- map（映射）

## 集合结构
![](/img/2018-08-27/collection-structure.png)

- Collection: Collection是最基本的集合接口，声明了适用于JAVA集合（只包括Set和List）的通用方法。
    - List: 有序结构,元素可重复
        - ArrayList: 数组实现, 查找快, 增删慢.
            - 由于是数组实现, 在增和删的时候会牵扯到数组增容, 以及拷贝元素. 所以慢.
            - 数组是可以直接按索引查找, 所以查找时较快
        - LinkedList: 链表实现, 增删快, 查找慢.
            - 由于链表实现, 增加时只要让前一个元素记住自己就可以, 删除时让前一个元素记住后一个元素, 后一个元素记住前一个元素.
            - 在添加和删除元素时具有比ArrayList更好的性能. 但在get与set方面弱于ArrayList.
        - Vector: 和ArrayList原理相同, 但线程安全, 效率略低.
            - 和ArrayList实现方式相同, 但考虑了线程安全问题, 所以效率略低(使用了synchronized).
    - Set: 无存储顺序, 不可重复
        - HashSet: 基于HashMap的key来实现。
        - TreeSet: 采用树结构实现(红黑树算法)
            - 元素是按顺序进行排列，但是add()、remove()以及contains()等方法都是复杂度为O(log (n))的方法。
            - 还提供了一些方法来处理排序的set, 如first(), last(), headSet(), tailSet()等等。
        - LinkedHashSet: 底层使用 LinkedHashMap 来保存所有元素。双链表结构。
-  Map: 键值对
    - HashMap: 键值对结构, 根据key的hashCode值put、get数据。最多一个key为null的记录, 允许多条value为null的记录。非线程安全。
    - TreeMap: 默认根据key升序排序, 也可以指定排序比较器。不允许key为null。非线程安全。
    - HashTable: key和value都不允许为null。线程安全。
    - LinkedHashMap: 链表结构，保存了记录的插入顺序。key和都都允许为空。非线程安全。

什么是List？
- 存储对象引用的容器。
- 特征：其元素以线性方式存储，集合中可以存放重复对象。

为什么要有集合List？不使用数组？
- 集合底层也是采用数组，但是数组长度是固定的，而集合长度会根据对象增长而变化。

集合List与数组的区别？
- 数组长度固定，集合长度可变。
- 数组可存储基本数据类型，集合只能存储对象。
- 定义一个数组只能存储单一类型的元素，定义一个集合什么对象都能存储。（List(Object)）

List接口主要实现类：
- ArrayList: 数组长度可变，随机访问速度快，插入、删除速度慢
- LinkedList: 链表实现，插入、删除速度快，随机访问速度慢
- Vector: 基本和ArrayList一致，只是每个方法加了synchronized关键字，线程安全
    - 默认扩容为原来的2倍
    - 可主动声明扩容时增加的容量大小，通过 Vector(int initialCapacity, int capacityIncrement)构造函数实现
- CopyOnWriteArrayList: 线程安全的ArrayLISt
    - 写入时copy底层数组副本，不影响原数组的读，修改完成后，一个原子操作将新的数组替换原数组
    - 删除时也一样，copy后修改再更新，不影响读，ReentrantLock保证原子性
    - 所有操作接口都采用锁实现 final ReentrantLock lock = this.lock;

## 集合List常用方法
- list.isEmpty();
- list.size();
- list.toArray();
- list.clear();
- list.set(int index, E element);
- contains
    - list.contains(Object o);
    - list.containsAll(Collection<?> c, Object o);
- add
    - list.add(Object e);
    - list.add(int index, Object element);
    - list.addAll(Collection c);
    - list.addAll(int index, Collection c);
- remove
    - list.remove(Object o);
    - list.remove(int index);
    - list.removeAll(Collection<?> c);
    - list.removeIf(Predicate filter);
- get
    - list.get(int index);
- indexOf
    - list.indexOf(Object o);
    - list.lastIndexOf(Object o);

> 注意：集合和数组中存放的都是对象的引用而非对象本身.

## 各种集合实例使用场景
- List：数据需要有序存储，并允许重复
    - ArrayList：查询较多
    - LinkedList：存取较多
    - Vector：需要线程安全
- set：无序保存，去重
    - HashSet：不需要排序
    - TreeMap：支持排序
    - LinkedHashSet：有序，去重

## List源码分析

#### ArrayList

- 基于数组实现，是一个动态数组，其容量能自动增长，类似于C语言中的动态申请内存，动态增长内存。
- 当元素数量达到集合阈值，自动扩容0.5倍。
    - 初始大小：未指定下默认值是10
    - 阈值：0.75
    - 扩容：1 + 0.5
    - 假如总容量：10，阈值7.5，当前元素数量6，再加一个元素，则当前元素为7，总容量为15，阈值为11.25
- 达到阈值，扩容原理是new一个新长度的数组，将元素copy过去。
- 非线程安全。只能用在单线程环境下，多线程环境下可以考虑用Collections.synchronizedList(List l)函数返回一个线程安全的ArrayList类，也可以使用concurrent并发包下的CopyOnWriteArrayList类。
- 在添加大量元素前，应用程序也可以使用ensureCapacity操作来增加ArrayList实例的容量，这可以减少递增式再分配的数量。

{% highlight java %}
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{% endhighlight %}

实现接口：
- List：集合接口
- RandomAccess：支持快速随机访问，实际上就是通过下标序号进行快速访问。
- Cloneable：支持克隆
- Serializable：支持序列化传输

**私有属性**
{% highlight java %}
//默认初始长度为10
private static final int DEFAULT_CAPACITY = 10;

//当调用无参数构造函数的时候默认给个空数组
private static final Object[] EMPTY_ELEMENTDATA = {};

//保存数据的数组
private transient Object[] elementData;

//arrayList的实际元素数量
private int size;
{% endhighlight %}

**构造函数**
{% highlight java %}
//自定义容量大小
public ArrayList(int initialCapacity) {}
//无参构造，默认容量大小为10
public ArrayList() {}
//传入一个Collection，将Collection的值copy到arrayList
public ArrayList(Collection<? extends E> c) {}
{% endhighlight %}


**扩容策略**
ArrayList扩容
- 数组进行扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组容量的增长大约是其原容量的1.5倍。
- 扩容操作的代价是很高的，在实际使用时，应该尽量避免数组容量的扩张。在初始化ArrayList的时候尽量预算下大致的容量需求，降低平凡调整容量的开销。

{% highlight java %}
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}

//每一次调用add，判断容量是否需要扩容
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        //第一次调用，集合为空，需要先胡世华
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }

    ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        //最小容量 - 元素数量 > 0 扩容
        grow(minCapacity);
}

private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    //ArrayList中初始长度为10，数组扩容1.5倍+1
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
{% endhighlight %}

因为底层采用数组存储，数组的特性是下标索引快，但是根据索引位置插入、删除对象，则会将当前索引位置及之后的所有元素响应向后移动一位。

看了默认大小和扩容机制，下面再来看看toArray()
{% highlight java %}
public Object[] toArray() {
    return Arrays.copyOf(elementData, size);
}

@SuppressWarnings("unchecked")
public <T> T[] toArray(T[] a) {
    if (a.length < size)
        // Make a new array of a's runtime type, but my contents:
        return (T[]) Arrays.copyOf(elementData, size, a.getClass());
    System.arraycopy(elementData, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}

public static <T> T[] copyOf(T[] original, int newLength) {
    return (T[]) copyOf(original, newLength, original.getClass());
}

public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
    @SuppressWarnings("unchecked")
    T[] copy = ((Object)newType == (Object)Object[].class)
        ? (T[]) new Object[newLength]
        : (T[]) Array.newInstance(newType.getComponentType(), newLength);
    System.arraycopy(original, 0, copy, 0,
                     Math.min(original.length, newLength));
    return copy;
}
{% endhighlight %}
list.toArray()底层是调用System.arraycopy()，将list的数组对象拷贝到一个新的数组里面。
toArray(T[] a)则比较特殊，如果传入数组的长度小于size，返回一个新的数组，大小为size，类型与传入数组相同。所传入数组长度与size相等，则将elementData复制到传入数组中并返回传入的数组。若传入数组长度大于size，除了复制elementData外，还将把返回数组的第size个元素置为空。

{% highlight java %}
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
{% endhighlight %}
clone实现是调用Object的clone(),然后将当前list对象的数组对象copy一份，放到拷贝好的v集合对象，清空modCount为0

**常用方法**
- set(int index, E element): 用指定的元素替代此列表中指定位置上的元素，并返回以前位于该位置上的元素。
- add(E e): 将指定的元素添加到此列表的尾部。
- add(int index, E element): 将指定的元素插入此列表中的指定位置。如果当前位置有元素，则向右移动当前位于该位置的元素以及所有后续元素（将其索引加1）。
- addAll(Collection<? extends E> c): 按照指定collection的迭代器所返回的元素顺序，将该collection中的所有元素添加到此列表的尾部。
- addAll(int index, Collection<? extends E> c): 从指定的位置开始，将指定collection中的所有元素插入到此列表中。
- get(int index): 返回数组中对应下标的元素。
- remove(int index): 先检查范围，修改modCount，保留将要被移除的元素，将移除位置之后的元素向前挪动一个位置，将list末尾元素置空（null），返回被移除的元素。
- remove(Object o): 移除此列表中首次出现的指定元素。
- removeRange(int fromIndex,int toIndex): 移除数组下标范围的元素，底层是将移除剩下的copy到新的数组。
- ensureCapacity(int minCapacity): 调整ArrayList中数组实际的容量，避免频繁扩容造成性能损耗。
- trimToSize(): 将底层数组的容量调整为当前列表保存的实际元素的大小的功能。

> 关键点记录

`fastRemove`跳过了判断边界的处理

remove(Object o)中通过遍历element寻找是否存在传入对象，一旦找到就调用fastRemove移除对象。

为什么找到了元素就知道了index，不通过remove(index)来移除元素呢？

因为fastRemove，因为找到元素就相当于确定了index不会超过边界，而且fastRemove并不返回被移除的元素。

下面是fastRemove的代码，基本和remove(index)一致。
{% highlight java %}
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work
}
{% endhighlight %}

`trimToSize()`缩减空间

由于elementData的长度会被拓展，size标记的是其中包含的元素的个数。

所以会出现size很小但elementData.length很大的情况，将出现空间的浪费。

trimToSize将返回一个新的数组给elementData，元素内容保持不变，length和size相同，节省空间。

关键字`transient`
transient修饰符让elementData无法自动序列化，这样的原因是，数组内存储的的元素其实只是一个引用，单单序列化一个引用没有任何意义，反序列化后这些引用都无法在指向原来的对象。

ArrayList使用writeObject()实现手工序列化数组内的元素。

#### Vector
Vector集合与ArrayList集合大体一致，只是Vector集合每个方法都用synchronized关键字修饰，故而是线程安全的。

{% highlight java %}
public Vector() {
    this(10);
}

public Vector(int initialCapacity) {
    this(initialCapacity, 0);
}

private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                     capacityIncrement : oldCapacity);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}
{% endhighlight %}
Vector比ArrayList直接，默认大小就是10，同样支持自定义长度。

ArrayList和Vector的区别：
- ArrayList在内存不够时默认是扩展50% + 1个，Vector是默认扩展1倍。
- Vector提供indexOf(obj, start)接口，ArrayList没有。
- Vector属于线程安全级别的，但是大多数情况下不使用Vector，因为线程安全需要更大的系统开销。

#### LinkedList
LinkedList底层是一个双向链表，查询慢，插入删除快。
每次查询都要从头或尾进行遍历，所以查询慢。
{% highlight java %}
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable{}
{% endhighlight %}

LinkedList的特性：
- 继承于AbstractSequentialList的双向链表。它也可以被当作堆栈、队列或双端队列进行操作。
- 实现 List 接口，能对它进行队列操作。
- 实现 Deque 接口，即能将LinkedList当作双端队列使用。
- 实现了Cloneable接口，即覆盖了函数clone()，能克隆。
- 支持序列化，能通过序列化去传输。
- 非线程安全。

链表结构比较好理解，就不多做解释了！

## Fail-Fast机制（快速失败）
> java容器的保护机制，能够防止多个进程/线程同时修改同一个容器的内容

在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出 `ConcurrentModificationException`（并发修改异常）

`ArrayList`也采用了快速失败的机制，通过记录[modCount]参数来实现。在面对并发的修改时，迭代器很快就会完全失败，而不是冒着在将来某个不确定时间发生任意不确定行为的风险。

## Fail-Safe机制（安全失败）
> 对集合结构的修改都会在copy的集合上进行修改，再更新。因此不会抛出 ConcurrentModificationException（并发修改异常）

采用安全失败机制的集合容器，在遍历时不时直接在集合内容上访问的，而是先复制原有集合内容，在拷贝的集合上进行遍历。

java.util.concurrent包下的容器都是安全失败，可以在多线程下并发使用，不会触发并发修改异常。
- ConcurrentHashMap
- CopyOnWriteArrayList
- CopyOnWriteArraySet
- ...

fail-safe机制有两个问题：
- 增删操作都会先复制集合，产生大量的无效对象，开销大，给gc增加压力。
- 无法保证读取的数据是目前原始数据结构中的数据。取出的可能是修改前的数据。

## 两种机制对比
Fail-Safe机制 不允许并发修改，不允许同时读写
Fail-Safe机制 允许并发修改，允许同时读写

## 使用注意事项
> 迭代删除List的某个元素

别使用如下方式，会报 `ConcurrentModificationException`
- for(Object o : list) {list.remove(o);}
建议使用 Iterator 方式删除元素
- Iterator<Object> iterator = list.iterator(); while(iterator.hasNext()){iterator.remove();}

> Arrays.asList() 数组转list
- asList得到的是原素组的一个视图，只允许查，不允许操作，操作会报错

> Collections.synchronizedList()：将不安全的List转换成安全的List

## 参考资料
[Java Platform SE 8][1]

[Java 集合Collection与List的详解](https://www.cnblogs.com/joyco773/p/6759981.html)

[Java集合框架之List---ArrayList与LinkedList源码分析](https://blog.csdn.net/oChangWen/article/details/50586260)


[1]:https://docs.oracle.com/javase/8/docs/api/?java/util/Collection.html