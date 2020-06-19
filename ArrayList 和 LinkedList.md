# ArrayList 和 LinkedList 

## 实现List接口

List 是一个有序的接口 可以包含重复元素 提供了按索引访问的方式，他继承了Collection接口。

<img src="/Users/miaozhiqiang/Desktop/集合链表框架.png" alt="集合链表框架" style="zoom:50%;" />



### ArrayList

一个可以动态增长和缩减的索引序列



1.ArrayList 继承了AbstractList抽象类，实现了List接口。

2.通过ArrayList成员变量可以看出内部实现是Object类型的数组结构。

3.默认大小为10。

4.由于实现类型为数组接口所以在操作大量的插入删除操作时，需要移动其他原油数据，效率较慢，对于随机访问效率较快。

5.数组从栈中分配空间，数组无需初始化，系统自动申请空间。数组在内存中是连续的。



### LinkedList

1.LinkedList继承AbstractSequentialList，实现List接口。

2.实现的数据结构为链表类型。

3.LinkedList 可以动态的进行存储空间分配，可以方便的实现插入删除操作。对于查询操作需要对整个链表进行遍历

4.链表从堆中分配空间

## 二者源码解读

### ArrayList

```
public class ArrayList<E> 
				extends AbstractList<E>
        implements List<E>, 
        RandomAccess,
        Cloneable, 
        java.io.Serializable
```

#### 1.成员变量





```
/**
 * Default initial capacity.
 * ArrayList默认数组大小
 */
private static final int DEFAULT_CAPACITY = 10;

/**
 * Shared empty array instance used for empty instances.
 * 用于空实例的共享空数组实例。
 */
private static final Object[] EMPTY_ELEMENTDATA = {};

/**
 * Shared empty array instance used for default sized empty instances. We
 * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
 * first element is added.
 *共享的空数组实例，用于默认大小的空实例。
 *我们将其与EMPTY_ELEMENTDATA区别开来，以了解添加第一个元素时需要填充多少。
 */
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

/**
 * The array buffer into which the elements of the ArrayList are stored.
 * The capacity of the ArrayList is the length of this array buffer. Any
 * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
 * will be expanded to DEFAULT_CAPACITY when the first element is added.
 *存储ArrayList元素的数组缓冲区。 
 *ArrayList的容量是此数组缓冲区的长度。添加第一个元素时，任何具有elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA 的空            	*ArrayList都将扩展为DEFAULT_CAPACITY。
 *ArrayList 的底层容器
 */
transient Object[] elementData; // non-private to simplify nested class access

/**
 * The size of the ArrayList (the number of elements it contains).
 * 此项为ArrayList 的数组大小
 * @serial
 */
private int size;
```

ArrayList 是基于动态数组的数据结构。如果没有指定大小则初始化大小为10 ，当增加元素时若超过原动态数组大小则扩大为原数组的1.5倍，然后把之前的数组拷贝到新的数组中。



添加方法

```
/**
 * Appends the specified element to the end of this list.
 *
 * @param e element to be appended to this list
 * @return <tt>true</tt> (as specified by {@link Collection#add})
 */
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
 }
private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
}
/**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
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
```