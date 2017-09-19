java常用集合类分析2_LinkedList

### 保存用的数据结构
内部使用Node来保存每一个元素和该元素的前后元素引用
```
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

LinkedList 会记录头元素和尾元素
```
    transient Node<E> first;
    transient Node<E> last;
```

### 扩容机制

### 检索元素(随机获取元素)
会判断这个index是在前半段还是后半段，进而采用从前遍历还是从后遍历.相对与ArrayList来说LinkedList的检索效率略低。

```
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
