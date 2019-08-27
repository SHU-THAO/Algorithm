　## HashMap

​	HashMap根据键的hashCode值存储数据，大多数情况下可以直接定位到它的值，因而具有很快的访问速度，但遍历顺序却不是确定的，HashMap最多只允许一条记录的键为null，允许多条记录的值为null，HashMap非线程安全，即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致，如果需要满足线程安全，可以用Collections的synchronizedMap方法使HashMap具有线程安全的能力，或者使用ConcurrentHashMap。大方向上，HashMap 是一个数组，然后数组中每个元素是一个单向链表，很多实体都是嵌套类Entry的实例，Entry包含四个属性：key、value、hash值和用于单向链表的next。

![img](https://img2018.cnblogs.com/blog/1344711/201907/1344711-20190731211747619-1858426907.jpg)



图为 Java 7中HashMap的结构

1. capacity：当前数组容量，始终保持 2^n，可以扩容，扩容后数组大小为当前的 2 倍。
2.  loadFactor：负载因子，默认为 0.75。 
3. *threshold：扩容的阈值，等于 capacity \* loadFactor。*

　　*上面是Java 7 对HashMap的实现，在JAVA 8中，利用了红黑树，所以在JAVA 8 是由数组+链表+红黑树组成。从JAVA 7中得知，在查找元素时候，可以根据hash值快速定位到数组具体的下标，但是后面的操作需要顺着链表一个一个的比较下去才能找到所需值，时间复杂度取决于链表的长度，为O(n)，为了降低这一部分处理的开销，在JAVA 8中，当链表中的元素超过8个之后，会将链表转换为红黑树，在这些位置进行查找的时候可降低时间复杂度 为O(logn)，*

*![img](https://img2018.cnblogs.com/blog/1344711/201907/1344711-20190731211908531-809506745.jpg)*

## HashSet

除开HashMap和Hashtable外，还有一个hash集合HashSet，有所区别的是HashSet不是key value结构，仅仅是存储不重复的元素，相当于简化版的HashMap，只是包含HashMap中的key而已

通过查看源码也证实了这一点，HashSet内部就是使用HashMap实现，只不过HashSet里面的HashMap所有的value都是同一个Object而已，因此HashSet也是非线程安全的，至于HashSet和Hashtable的区别，HashSet就是个简化的HashMap的

## TreeMap

TreeMap 的实现就是红黑树数据结构，也就说是一棵自平衡的排序二叉树，这样就可以保证当需要快速检索指定节点。

对于 TreeMap 而言，它采用一种被称为“红黑树”的排序二叉树来保存 Map 中每个 Entry —— 每个 Entry 都被当成“红黑树”的一个节点对待。例如对于如下程序而言：

## TreeSet

TreeSet类似与HashSet，内部都是通过对应的Map来实现。

是使用二叉树的原理对新ADD()的对象按照指定的顺序排序（升序、降序），每增加一个对象都会进行排序，将对象插入二叉树指定的位置，在Integer和String对象都可以进行默认的TreeSet排序，而自定义的对象不可以，必须实现Comparable接口，并且覆写相应的compareTo()函数才可以正常的使用TreeSet()。注意：在覆写compare()函数时，要返回相应的值才能使TreeSet按照一定的规则来排序；在比较对象之间的顺序，如果该对象小于、等于或大于指定对象，则分别返回负整数、零或正整数。

TreeSet：

　　排列无序、不可重复，内部使用TreeMap的SortedSet

**floor(E e)** **方法返回在这个集合中小于或者等于给定元素的最大元素，如果不存在这样的元素,返回null.**

**ceiling(E e) 方法返回在这个集合中大于或者等于给定元素的最小元素，如果不存在这样的元素,返回null.**



通常，有两种最广泛使用的集合：`散列集合`(Hash Set)和`树集合`(Tree Set)。

`树集合`, Java中的`Treeset`或者C++中的`set`，是由高度平衡的二叉搜索树实现的。因此，搜索、插入和删除的时间复杂度都是`O(logN)`。

`散列集合`, Java中的`HashSet`或者C++中的`unordered_set`，是由哈希实现的, 但是平衡二叉搜索树也起到了至关重要的作用。当存在具有相同哈希键的元素过多时，将花费`O(N)`时间复杂度来查找特定元素，其中N是具有相同哈希键的元素的数量。 通常情况下，使用高度平衡的二叉搜索树将把时间复杂度从 `O(N) `改善到 `O(logN)`。

哈希集和树集之间的本质区别在于树集中的键是`有序`的。