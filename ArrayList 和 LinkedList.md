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

## 源码解读

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

ArrayList 是基于动态数组的数据结构。如果没有指定大小则初始化大小为10 



**添加方法**

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
 //
private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
        //扩容
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
        //记录之前数组长度
        int oldCapacity = elementData.length;
        //记录新数组大小 = 旧数组长度 + 旧数组长度大小的一般
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //判断扩容后的数组能否装下minCapacity个元素
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
         //判断扩容后数组长度是否大于最大值(Integer.MAX_VALUE - 8) 若大于最大值则交给hugeCapacity 进行处理
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        //复制数组（浅复制）
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
    //判断若minCapacity个元素数量是否大于数组最大长度，若大于最大长度则使用Int最大值，否则取数组最大长度 
  private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

总结：

1.先判断添加一个元素是否会导致数组溢出

2.判断若原数组为空则添加一个元素设置数组为默认长度10，判断是否溢出，若溢出则进行扩容，扩容后的大小为原数组的1.5倍

3.并判断要添加元素后的数组大小是否大于数组长度最大值，若超过数组长度最大值则设置扩容大小为int最大值，否则设置成为数组最大值。

4.最后通过Array.copyOf进行浅复制

**查询方法**

```
/**
 * Returns the element at the specified position in this list.
 *
 * @param  index index of the element to return
 * @return the element at the specified position in this list
 * @throws IndexOutOfBoundsException {@inheritDoc}
 */
public E get(int index) {
//判断需查询索引的大小是否超过数组大小，若大于则抛出IndexOutOfBoundsException异常
    rangeCheck(index);

    return elementData(index);
}

 E elementData(int index) {
        return (E) elementData[index];
    }
```

通过下标来获取数组中的元素

**删除方法**

