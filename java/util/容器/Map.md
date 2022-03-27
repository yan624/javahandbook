## Map
下图是 `java.util` （左侧）和 `java.util.concurrent` （右侧）包中所有的 `Map` 类（除去一些内部类）。

![Map](img/Map.png)

**`AbstractMap`** 提供了 `Map` 接口的基本实现，以减小后续实现的工作量。

**`HashMap`** 存储的是键值对，本质上是 `Node` 数组，能够以常量时间进行一些基本操作（`get`，`put`），前提是哈希函数能够将元素分散到哈希表中。`HashMap` 不是同步的。

该实现在哈希冲突大于等于 8 时，将哈希表中的链表转为红黑树，在小于等于 6 时，转回链表。

**`LinkedHashMap`** 使用双向链表管理所有的条目，使得所有条目的顺序都是插入顺序。

**`TreeMap`** 用键排序以某种顺序存储键值对，本质上是一颗红黑树（注意不是数组，没有用 hash）。

**`Hashtable`** 及其子类 **`Properties`** 是 JDK1.0 引入的“Map”，是同步的，目前基本已经废弃。

**`WeakHashMap`**

**`IdentityHashMap`**

### ConcurrentMap
[还不懂 ConcurrentHashMap ？这份源码分析了解一下](https://www.wdbyte.com/2020/04/jdk/concurrent-hashmap/#_1-concurrenthashmap-1-7)

