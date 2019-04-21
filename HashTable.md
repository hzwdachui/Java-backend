# HashTable

哈希表（Hash Table，也叫散列表），是根据关键码值 (Key-Value) 而直接进行访问的数据结构。也就是说，**它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。**

- 哈希函数
- 冲突解决

## 哈希函数
确定存储位置和关键字之间的对应关系的函数：  
常用取余数的方法（保证了value小于哈希表的大小）

	hashcode("abcd") = (ascii(a) * 333 + ascii(b) * 332 + ascii(c) *33 + ascii(d)) % HASH_SIZE 
	
	                              = (97* 333 + 98 * 332 + 99 * 33 +100) % HASH_SIZE
	
	                              = 3595978 % HASH_SIZE
## 冲突
当两个不同的输入值对应一个输出值时，就会产生“碰撞”，这个时候便需要解决冲突。

影响冲突的因素：

- [1]散列函数是否均匀；

- [2]处理冲突的方法；

- [3]散列表的装填因子。

用扰动函数使其更均匀；

解决冲突常见开放定址法，**链地址法**，建立公共溢出区等

- 链地址法
为每个 Hash 值建立一个单链表，当发生冲突时，将记录插入到链表中。
- 开放地址法
就是冲突了就向后挪

散列表的装填因子：  
α=填入表中的元素个数 /散列表的长度  
**规律**： α越大，填入表中的元素较多，产生冲突的可能性就越大（**冲突的多查找效率就低**）；   
超过阈值就resize

## HashMap、Hashtable、ConcurrentHashMap的原理与区别
- 定义区别
- 线程是否安全
- put方法如何判断重复
- JDK7和JDK8 中HashMap的实现有什么区别？（?）
- HashMap的长度为什么是2的幂次方？(?)

### HashTable
- 底层数组+链表实现，无论key还是value都不能为null
- **线程安全**，实现线程安全的方式是在修改数据时锁住整个HashTable，效率低，ConcurrentHashMap做了相关优化
- hash函数：（计算index的方法）：index = (hash & 0x7FFFFFFF) % tab.length
- 初始size为11，扩容：newsize = oldsize*2+1

### HashMap
- 底层数组+链表实现，可以存储null键和null值
- **线程不安全**
- 初始size为16，扩容：newsize = oldsize*2，size一定为2的n次幂
- 扩容针对整个Map，每次扩容时，原来数组中的元素依次重新计算存放位置，并重新插入
- **插入元素后才判断该不该扩容，有可能无效扩容**（插入后如果扩容，如果没有再次插入，就会产生无效扩容）
当Map中元素总数超过Entry数组的75%，触发扩容操作，为了减少链表长度，元素分配更均匀
- 计算index方法：index = hash & (tab.length – 1)

### ConcurrentHashMap
- 底层采用分段的数组+链表实现
- **线程安全**
- 通过把整个Map分为N个Segment，可以提供相同的线程安全，但是效率提升N倍，默认提升16倍。(读操作不加锁，由于HashEntry的value变量是 volatile的，也能保证读取到最新的值。)
- Hashtable的synchronized是针对整张Hash表的，即每次锁住整张表让线程独占，ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术
有些方法需要跨段，比如size()和containsValue()，它们可能需要锁定整个表而而不仅仅是某个段，这需要按顺序锁定所有段，操作完毕后，又按顺序释放所有段的锁
扩容：段内扩容（段内元素超过该段对应Entry数组长度的75%触发扩容，不会对整个Map进行扩容），**插入前检测需不需要扩容，有效避免无效扩容**

### 对比
#### HashTable and HashMap
- 都实现了Map接口
- 默认容量不同（HashTable 11，HashMap 16），扩容不同
- 线程安全：
	- HashTable是线程安全的，是同步的，这意味着Hashtable是线程安全的，多个线程可以共享一个Hashtable
	- HashMap是非同步的。
- null
	- HashTable不接受null
	- HashMap中，可以有一个Key为null，value可以为null
- Iterator（？？？）
	- HashTable：enumerator迭代器
	- HashMap：fail-fast迭代器
- 由于Hashtable是线程安全的也是synchronized，所以在单线程环境下它比HashMap要慢。如果你不需要同步，只需要单一线程，那么使用HashMap性能要好过Hashtable。
- HashMap不能保证随着时间的推移Map中的元素次序是不变的。（？？？）
- **我们能否让HashMap同步**？  
HashMap可以通过下面的语句进行同步：
``Map m = Collections.synchronizeMap(hashMap);``
#### HashTable and ConcurrentHashhMap
- Java5提供了ConcurrentHashMap，它是HashTable的替代，比HashTable的扩展性更好。
- ConcurrentHashMap是由**Segment**数组结构和**HashEntry**数组结构组成。Segment是一个可重入锁（ReentrantLock）。当对HashEntry数组的数据进行修改时，必须首先获得与它对应的segment锁。
- ConcurrentHashMap提供了与Hashtable不同的锁机制： 
	- Hashtable中采用的锁机制是一次锁住整个hash表，从而在同一时刻只能由一个线程对其进行操作
	- 而ConcurrentHashMap**锁分段技术**：一次锁住一个桶（一部分数据，默认16个桶）。线程间就不会存在锁竞争，从而可以有效提高并发访问效率。

# 问题HashMap
## hashmap的主要参数都有哪些？
- 1）**桶（capacity）容量**，即数组长度：  
DEFAULT_INITIAL_CAPACITY=1<<4；默认值为16  
即在不提供有参构造的时候，声明的hashmap的桶容量；  
- 2）**MAXIMUM_CAPACITY** = 1 << 30;  
极限容量，表示hashmap能承受的最大桶容量为2的30次方，超过这个容量将不再扩容，让hash碰撞起来吧！  
- 3）**static final float DEFAULT_LOAD_FACTOR = 0.75f;**  
负载因子（loadfactor，默认0.75）  
计算HashMap的实时装载因子的方法为：size/capacity
- 4）**int threshold;阈值**（??）  
阈值算法为capacity*loadfactory，大致当map中entry数量大于此阈值时进行扩容（1.8）  
- 5）**transient Entry<K,V>[] table = (Entry<K,V>[]) EMPTY_TABLE;**（默认为空{}）

## hashmap的数据结构是什么样子的？自己如何实现一个hashmap？
核心的数据结构，即所谓的数组+链表的部分。在hashmap中的主要表现形式为一个table，类型为Entry<K,V>[] table  
首先是一个Entry型的数组，Entry为hashmap的内部类：
## hash计算规则是什么？（哈希函数的实现）

## 说说hashmap的存取过程？（Hash如何实现建立与查找）
### put 过程（JDK1.8）
- 对 Key 求 Hash 值，然后再计算地址（哪个bucket）
- 如果没有碰撞，直接放入桶中（碰撞的意思是计算得到的 Hash 值相同，需要放到同一个 bucket 中）
- 如果碰撞了，以链表插入
- 如果链表长度超过阀值（TREEIFY THRESHOLD==8），就把链表转成红黑树，链表长度低于6，就把红黑树转回链表（？？？）
- 如果节点已经存在就替换旧值
- 如果桶满了（容量16*加载因子0.75），就需要 resize（扩容2倍后重排）
- 
### get 过程
- 当我们调用 get() 方法，HashMap 会使用键对象的 hashcode 找到 bucket 位置，找到 bucket 位置之后，会调用 keys.equals() 方法去找到链表中正确的节点，最终找到要找的值对象。

## 说说hashmap如何处理碰撞的，或者说说它的扩容？
- **扰动函数**可以减少碰撞（存值的时候并不是直接使用的key的hashcode，而是通过扰动函数算出了一个新的hash值）
- 扩容来减少碰撞

## 树结构转化
之所以选择红黑树是为了解决二叉查找树的缺陷：二叉查找树在特殊情况下会变成一条线性结构（这就跟原来使用链表结构一样了，造成层次很深的问题），遍历查找会非常慢。而红黑树在插入新数据后可能需要通过左旋、右旋、变色这些操作来保持平衡。引入红黑树就是为了查找数据快，解决链表查询深度的问题。我们知道红黑树属于平衡二叉树，为了保持“平衡”是需要付出代价的，但是该代价所损耗的资源要比遍历线性链表要少。所以当长度大于8的时候，会使用红黑树；如果链表长度很短的话，根本不需要引入红黑树，引入反而会慢。

> 红黑树  
> 
- 自平衡二叉查找树，O(log n)时间内做查找，插入和删除
- 插入和删除操作时通过对树进行**旋转操作**（**修改树中某些结点的颜色及指针结构**）保持二叉查找树的平衡
- 性质
	- 只有红色和黑色
	- 根节点是黑色
	- 每个叶节点（NIL节点（？？），空节点）是黑色的。
	- 红色节点的子节点必为黑色
	- 从根节点到叶节点或空子节点的每条路径，必须包含相同数目的黑色节点（**即相同的黑色高度**）