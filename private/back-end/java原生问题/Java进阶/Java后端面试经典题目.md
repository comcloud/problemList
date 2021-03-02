# `Java`后端面试经典题目

## 一：java集合

### **1，对Java集合框架的理解。ArrayList和LinkedList的区别和优缺点，以及使用场景。扩容因子了解吗？分别是多少。**

> 　**Java集合框架**
>
> 集合可以看作是一种容器，用来存储对象信息。所有集合类都位于java.util包下，但支持多线程的集合类位于java.util.concurrent包下。
>
> 　　数组与集合的区别如下：
>
> 　　1）数组长度不可变化而且无法保存具有映射关系的数据；集合类用于保存数量不确定的数据，以及保存具有映射关系的数据。
>
> 　　2）数组元素既可以是基本类型的值，也可以是对象；集合只能保存对象。
>
> 　　Java集合类主要由两个根接口Collection和Map派生出来的，Collection派生出了三个子接口：List、Set、Queue（Java5新增的队列），因此Java集合大致也可分成List、Set、Queue、Map四种接口体系，（注意：Map不是Collection的子接口）。
>
> 　　其中List代表了有序可重复集合，可直接根据元素的索引来访问；Set代表无序不可重复集合，只能根据元素本身来访问；Queue是队列集合；Map代表的是存储key-value对的集合，可根据元素的key来访问value。
>
> **ArrayList和LinkedList的区别和优缺点，以及使用场景**
>
> 　　**区别：**
>
> 1、ArrayList是实现了基于动态数组的数据结构，LinkedList是基于链表结构。
> 2、对于随机访问的get和set方法，ArrayList要优于LinkedList，因为LinkedList要移动指针。
> 3、对于新增和删除操作add和remove，LinkedList比较占优势，因为ArrayList要移动数据。
> **优缺点：**
>
> 1、对ArrayList和LinkedList而言，在列表末尾增加一个元素所花的开销都是固定的。对ArrayList而言，主要是在内部数组中增加一项，指向所添加的元素，偶尔可能会导致对数组重新进行分配；而对LinkedList而言，这个开销是 统一的，分配一个内部Entry对象。
> 2、在ArrayList集合中添加或者删除一个元素时，当前的列表移动元素后面所有的元素都会被移动。而LinkedList集合中添加或者删除一个元素的开销是固定的。
> 3、LinkedList集合不支持 高效的随机随机访问（RandomAccess），因为可能产生二次项的行为。
> 4、ArrayList的空间浪费主要体现在在list列表的结尾预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗相当的空间
>
> **使用场景**
>
> ArrayList使用在查询比较多，但是插入和删除比较少的情况，而LinkedList用在查询比较少而插入删除比较多的情况
>
> **扩容因子了解吗？分别是多少**
>
> 集合有初始容量，当容量大于一定长度L时，集合需要进行扩容；而该长度L等于当前长度 * 扩容因子。
>
> HashMap和HashSet扩容因子为：0.75，ArrayList和Vector是1。

### **2，HashMap和HashTable的区别，优缺点。HashMap和ConcurrentHashMap的区别。**

> 　**HashMap：**
>
> 　　HashMap是基于哈希表实现的，每一个元素是一个key-value对，其内部通过单链表解决冲突问题，容量不足（超过了阀值）时，同样会自动增长。
>
> 　　HashMap是非线程安全的，只是用于单线程环境下，多线程环境下可以采用concurrent并发包下的concurrentHashMap。
>
> 　　HashMap 实现了Serializable接口，因此它支持序列化，实现了Cloneable接口，能被克隆。
>
> 　　HashMap存数据的过程是：
>
> 　　HashMap内部维护了一个存储数据的Entry数组，HashMap采用链表解决冲突，每一个Entry本质上是一个单向链表。当准备添加一个key-value对时，首先通过hash(key)方法计算hash值，然后通过indexFor(hash,length)求该key-value对的存储位置，计算方法是先用hash&0x7FFFFFFF后，再对length取模，这就保证每一个key-value对都能存入HashMap中，当计算出的位置相同时，由于存入位置是一个链表，则把这个key-value对插入链表头。
>
> 　　HashMap中key和value都允许为null。key为null的键值对永远都放在以table[0]为头结点的链表中。
>
> ***\*HashMap和HashTable的区别，优缺点：\****
>
> 1、Hashtable继承自Dictionary类，而HashMap继承自AbstractMap类。但二者都实现了Map接口。
>
> 2、Hashtable 线程安全，因为它每个方法中都加入了Synchronize。HashMap线程不安全的。
>
> 3、 HashMap把Hashtable的contains方法去掉了，Hashtable则保留了contains
>
> 4、Hashtable中，key和value都不允许出现null值。HashMap中，null可以作为键，这样的键只有一个；可以有一个或多个键所对应的值为null。
>
> 5、Hashtable、HashMap都使用了 Iterator。而由于历史原因，Hashtable还使用了Enumeration的方式。
>
> 6、哈希值的使用不同，HashTable直接使用对象的hashCode。而HashMap重新计算hash值。
>
> 7、 HashTable在不指定容量的情况下的默认容量为11，而HashMap为16，Hashtable不要求底层数组的容量一定要为2的整数次幂，而HashMap则要求一定为2的整数次幂。Hashtable扩容时，将容量变为原来的2倍加1，而HashMap扩容时，将容量变为原来的2倍
>
> **下面直接来干货，先说这三个Map的区别：**
>
> **HashTable**
>
> - - 底层数组+链表实现，无论key还是value都**不能为null**，线程**安全**，实现线程安全的方式是在修改数据时锁住整个HashTable，效率低，ConcurrentHashMap做了相关优化
>   - 初始size为**11**，扩容：newsize = olesize*2+1
>   - 计算index的方法：index = (hash & 0x7FFFFFFF) % tab.length
>
> **HashMap**
>
> - - 底层数组+链表实现，可**以存储null键和null值**，线程**不安全**
>   - 初始size为**16**，扩容：newsize = oldsize*2，size一定为2的n次幂
>   - 扩容针对整个Map，每次扩容时，原来数组中的元素依次重新计算存放位置，并重新插入
>   - 插入元素后才判断该不该扩容，有可能无效扩容（插入后如果扩容，如果没有再次插入，就会产生无效扩容）
>   - 当Map中元素总数超过Entry数组的75%，触发扩容操作，为了减少链表长度，元素分配更均匀
>   - 计算index方法：index = hash & (tab.length – 1)
>
> HashMap的初始值还要考虑加载因子:
>
> - -  **哈希冲突**：若干Key的哈希值按数组大小取模后，如果落在同一个数组下标上，将组成一条Entry链，对Key的查找需要遍历Entry链上的每个元素执行equals()比较。
>   - **加载因子**：为了降低哈希冲突的概率，默认当HashMap中的键值对达到数组大小的75%时，即会触发扩容。因此，如果预估容量是100，即需要设定100/0.75＝134的数组大小。
>   - **空间换时间**：如果希望加快Key查找的时间，还可以进一步降低加载因子，加大初始大小，以降低哈希冲突的概率。
>
> HashMap和Hashtable都是用hash算法来决定其元素的存储，因此HashMap和Hashtable的hash表包含如下属性：
>
> - - 容量（capacity）：hash表中桶的数量
>   - 初始化容量（initial capacity）：创建hash表时桶的数量，HashMap允许在构造器中指定初始化容量
>   - 尺寸（size）：当前hash表中记录的数量
>   - 负载因子（load factor）：负载因子等于“size/capacity”。负载因子为0，表示空的hash表，0.5表示半满的散列表，依此类推。轻负载的散列表具有冲突少、适宜插入与查询的特点（但是使用Iterator迭代元素时比较慢）
>
> 除此之外，hash表里还有一个“负载极限”，“负载极限”是一个0～1的数值，“负载极限”决定了hash表的最大填满程度。当hash表中的负载因子达到指定的“负载极限”时，hash表会自动成倍地增加容量（桶的数量），并将原有的对象重新分配，放入新的桶内，这称为rehashing。
>
> HashMap和Hashtable的构造器允许指定一个负载极限，HashMap和Hashtable默认的“负载极限”为0.75，这表明当该hash表的3/4已经被填满时，hash表会发生rehashing。
>
> “负载极限”的默认值（0.75）是时间和空间成本上的一种折中：
>
> - - 较高的“负载极限”可以降低hash表所占用的内存空间，但会增加查询数据的时间开销，而查询是最频繁的操作（HashMap的get()与put()方法都要用到查询）
>   - 较低的“负载极限”会提高查询数据的性能，但会增加hash表所占用的内存开销
>
> 程序猿可以根据实际情况来调整“负载极限”值。
>
> **ConcurrentHashMap**
>
> - - 底层采用分段的数组+链表实现，线程**安全**
>   - 通过把整个Map分为N个Segment，可以提供相同的线程安全，但是效率提升N倍，默认提升16倍。(读操作不加锁，由于HashEntry的value变量是 volatile的，也能保证读取到最新的值。)
>   - Hashtable的synchronized是针对整张Hash表的，即每次锁住整张表让线程独占，ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术
>   - 有些方法需要跨段，比如size()和containsValue()，它们可能需要锁定整个表而而不仅仅是某个段，这需要按顺序锁定所有段，操作完毕后，又按顺序释放所有段的锁
>   - 扩容：段内扩容（段内元素超过该段对应Entry数组长度的75%触发扩容，不会对整个Map进行扩容），插入前检测需不需要扩容，有效避免无效扩容
>
> Hashtable和HashMap都实现了Map接口，但是Hashtable的实现是基于Dictionary抽象类的。Java5提供了ConcurrentHashMap，它是HashTable的替代，比HashTable的扩展性更好。
>
> HashMap基于哈希思想，实现对数据的读写。当我们将键值对传递给put()方法时，它调用键对象的hashCode()方法来计算hashcode，然后找到bucket位置来存储值对象。当获取对象时，通过键对象的equals()方法找到正确的键值对，然后返回值对象。HashMap使用链表来解决碰撞问题，当发生碰撞时，对象将会储存在链表的下一个节点中。HashMap在每个链表节点中储存键值对对象。当两个不同的键对象的hashcode相同时，它们会储存在同一个bucket位置的链表中，可通过键对象的equals()方法来找到键值对。如果链表大小超过阈值（TREEIFY_THRESHOLD,8），链表就会被改造为树形结构。
>
> 在HashMap中，null可以作为键，这样的键只有一个，但可以有一个或多个键所对应的值为null。**当get()方法返回null值时，即可以表示HashMap中没有该key，也可以表示该key所对应的value为null**。因此，在HashMap中不能由get()方法来判断HashMap中是否存在某个key，应该用**containsKey()**方法来判断。而在Hashtable中，无论是key还是value都不能为null。
>
> Hashtable是线程安全的，它的方法是同步的，可以直接用在多线程环境中。而HashMap则不是线程安全的，在多线程环境中，需要手动实现同步机制。
>
> Hashtable与HashMap另一个区别是HashMap的迭代器（Iterator）是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构（增加或者移除元素），将会抛出ConcurrentModificationException，但迭代器本身的remove()方法移除元素则不会抛出ConcurrentModificationException异常。但这并不是一个一定发生的行为，要看JVM。