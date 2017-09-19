

## 常用集合类：List，ArrayList，LinkedList


##ArrayList

默认的初始化容量是10
```
/**
     * Default initial capacity.
     */
    private static final int DEFAULT_CAPACITY = 10;
```

最大的长度
```
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
```

使用数组结构来保存列表中的元素

```
/**
     * The array buffer into which the elements of the ArrayList are stored.
     * The capacity of the ArrayList is the length of this array buffer. Any
     * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     * will be expanded to DEFAULT_CAPACITY when the first element is added.
     */
    transient Object[] elementData; // non-private to simplify nested class access
```
ArrayList有三个构造器

第一个构造器会根据传入的容量进行初始化,如果大于0则初始化一个对应容量的数组，如果铜梁为0则使用默认的空数组进行初始化，小于0会触发参数异常

```
/**
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
```
第二个构造函数使用默认容量的空数组进行初始化,其实使用的是一个共享的空数组

```
/**
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```

第三个构造函数是使用一个集合进行初始化，如果参数集合的长度为0，则还是使用默认的空数组进行初始化，否则将集合转化为数组，然后复制到内部数组中。
```
    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

### ArrayList元素数增长背后

添加元素的两个方法,这两个方法的差别就是插入到末位还是插入到指定的位置，插入指定元素的话需要在扩容以后调用本地方法进行数组拷贝。


如果使用的空数组初始化，前期添加元素的时候如果，数量小于10默认会按照10个进行初始化（对应的是无参数的构造函数），

size的增长是通过自增来实现的

```
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount
        elementData[size++] = e;
        return true;
    }

    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
    }
```

增加多个元素（集合）
```
    public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount
        System.arraycopy(a, 0, elementData, size, numNew);
        size += numNew;
        return numNew != 0;
    }
```

插入前需要确保内部数组是否放得下当前的队列，所以使用**size + 1** 进行探测，
```
    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;
        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
```

如果容量不够用，则需要根据所需的最小容量进行进行grow，先增加到原容量的1.5倍

```
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
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```