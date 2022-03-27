## List
下图是 `java.util` 包中所有的 `List` 类（除去一些内部类）。可以看出其实也没多少类。

![List](img/List.png)

**`AbstractList`** 提供了 `List` 接口的基本实现，以减小后续实现的工作量，后续实现通常支持“随机访问”数据存储（比如列表）。对于顺序访问的数据（比如链表），应优先使用 `AbstractSequentialList`（未显示在图中，其子类基本就一个 `LinkedList`）而不是此类。

**`LinkedList`** 是 `List` 和 `Deque` 接口的双向链表（Doubly-linked list）实现。实现所有的列表操作，允许所有的元素（包括 null）。

该实现不是同步的。如果多个线程同时访问链表，并且至少有一个线程修改了链表的结构，则必须在外部对其同步。（结构修改是添加或删除，设置一个已有结点的值不算）这通常在封装链表的对象上进行同步。如果不存在，链表应该由 `Collections.synchronizedList` 方法包装。这最好在创建时就做好，以防止意外不同步地访问链表：

```java
List list = Collections.synchronizedList(new LinkedList(...));
```

**`ArrayList`** 是 `List` 接口的可变大小数组的实现。实现所有的列表操作，允许所有的元素（包括 null）。

**`SubList`** 返回列表的一个从 `fromIndex` 到 `toIndex` 的视图（view）。**注意返回的不是拷贝，并且视图只能“看”。**

**`RandomAccessSubList`** 没有具体的含义，只是实现了 `RandomAccess` 类。`RandomAccess` 是一个标记接口，它只是为了提升代码的性能。参考 `Collections.fill()` 方法，它会判断一个列表是不是 `RandomAccess`，如果是的话会用更高效的填充方式。值得注意的是 `RandomAccess` 应该被翻译为“任意访问”，跟“随机”没多大关系，不要被接口名误导了。`ArrayList` 也实现了这个接口，因为数组是可以“random access”的。然而链表就不行。

**`Vector`** 被设计为同步的。目前基本处于废弃的状态（但并没有被标记为废弃），java 不推荐使用。

**`Stack`** 表现为后进先出（LIFO）的栈对象。`Deque` 接口及其实现提供了一组更完整、一致的后进先出栈操作实现，应该优先使用 `Deque` 的实现。

## Queue
下图是 `java.util` 包中所有的 `Queue` 类（除去一些内部类）。

![Queue](img/Queue.png)

**`AbstractQueue`** 提供了 `Queue` 接口的基本实现，以减小后续实现的工作量。不支持插入 null。

**`Deque`** 接口指的是双向队列，即“double ended queue”，其有两种实现，分别是双向链表（基于链表） **`LinkedList`** 和双向列表（基于数组） **`ArrayDeque`**。

**`PriorityQueue`** 指的是优先队列，即二叉堆。该队列的头（堆顶）是序列中最小的元素，即小顶堆。

### BlockingQueue

## Set
下图是 `java.util` 包中所有的 `Set` 类（除去一些内部类）。可以看出其实也没多少类。

![Set](img/Set.png)

由于 `Set` 的底层实现是 `Map`，因此具体的细节放到 Map 一节描述。

**`AbstractSet`** 提供了 `Set` 接口的基本实现，以减小后续实现的工作量。

**`HashSet`** 是 `Set` 接口的实现，支持哈希表（实际上是一个 `HashMap` 实例）。它不保证集合的迭代顺序，尤其是，它不保证顺序每次都一样。该类允许 null 元素。

**`LinkedHashSet`** 继承了 `HashSet`，	拥有可预测的顺序，实际上维护的是一个 `LinkedHashMap` 实例。它的内部只是调整了一些参数，没有太多代码。该实现不同于 `Hashset`，其为所有的项维护了一个双向的链表。

该实现让客户端免于 `HashSet` 的未指定且混乱的排序，也不会增加与 `TreeSet` 相关的成本。

**`EnumSet`** 是 `Set` 的一个特例，其维护所指定枚举类型的所有枚举值。它有两个私有（`default` 修饰符）实现，`RegularEnumSet`（支持 64 个元素）和 `JumboEnumSet`（支持超过 64 个元素）。

**`TreeSet`** 基于 `TreeMap`。该实现不是同步的。

