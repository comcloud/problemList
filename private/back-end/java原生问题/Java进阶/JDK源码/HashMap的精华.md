# `HashMap`的精华

## 一：引言

> `HashMap`有两个是他的核心，一是默认值，二是结构设计，今天我们这里就来聊聊他们(本文采用的`JDK`版本是1.8)

**首先我们从他的默认值入手**

## 二：开始入手

### **默认值**

> `HashMap`的默认值分别如下：
>
> - ```java
>   /**
>    * hash表的默认容量，源码中标注必须是2的幂次方，具体原因我们稍后在下面讲解hash算法时候再说明
>    */
>   static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
>   ```
>
> - ```java
>   /**
>    * 最大容量, 如果隐式指定了更高的值，则使用由带参数的构造函数之一.
>    * 必须是2的幂 并且他小于等于1<<30.
>    */
>   static final int MAXIMUM_CAPACITY = 1 << 30;
>   ```
>
> - ```java
>   /**
>    * 负载因子，这个是参考了空间使用率和时间复杂度后折中设置的值，当然可以认为是0<loader<=1
>    */
>   static final float DEFAULT_LOAD_FACTOR = 0.75f;
>   ```
>
> - ```java
>   /**
>    * 这是一个树形化阈值，必须大于2，并且至少应为8，这个阈值是做啥的呢，就是链表跟树转换的阈值，一个列表长度超过这个阈值时候就要转换为红黑树(在JDK1.8以后)
>    */
>   static final int TREEIFY_THRESHOLD = 8;
>   ```
>
> - ```java
>   /**
>    * 这个是反树形化阈值，跟上面的一个相反
>    */
>   static final int UNTREEIFY_THRESHOLD = 6;
>   ```
>
> - ```java
>   /**
>    * 最小的表容量(超过应被树形化，如果这个bin中的节点太多就被重置大小)
>    * 应该至少4 * TREEIFY_THRESHOLD以避免在调整大小和树形化阈值之间冲突
>    * Should be at least 4 * TREEIFY_THRESHOLD to avoid conflicts
>    * between resizing and treeification thresholds.
>    */
>   static final int MIN_TREEIFY_CAPACITY = 64;
>   ```

**这里我仍旧想把部分计算方法纳入到这个默认值之中**

- **hash算法**

~~~java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
//下面是 put() 方法中的一行代码，n为哈希桶数组长度，hash为前一步确定的hash值
p = tab[i = (n - 1) & hash]
~~~

> 这时候我们再去看看上面最小容量为什么是`power of two`,这个是因为设计使然，倘若n为偶数，那么(n-1)将会是奇数，换算为二进制则最后一位是0，但是这里要做一个&，那么将永远有一个位置计算不到，并且我们再去看下面的图，table表下标使用`(n-1) & hash`，实际上是取代了曾经的%运算，位运算效率更高效一些
>
> 并且我们在去看下面的计算方法，只有hash值的低4位参与了运算。
> 这样做很容易产生碰撞。设计者权衡了`speed, utility, and quality`，将高16位与低16位异或来减少这种影响。设计者考虑到现在的`hashCode`分布的已经很不错了，而且当发生较大碰撞时也用树形存储降低了冲突。仅仅异或一下，既减少了系统的开销，也不会造成的因为高位没有参与下标的计算(table长度比较小时)，从而引起的碰撞。
>
> ![img](https://upload-images.jianshu.io/upload_images/18499914-08b5602de447673d.png?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

- `tableSizeFor`，用于找到大于等于`initialCapacity`的最小的2的幂

~~~java
static final int tableSizeFor(int cap) {
    //放置此时cap已经是2的幂，如果cap已经是2的幂， 又没有执行这个减1操作，则执行完后面的几条无符号右移操作之后，返回的capacity将是这个cap的2倍
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
//this.threshold = tableSizeFor(initialCapacity);
//这个是在HashMap的构造方法使用方法，threshold就是阈值，这里我来说明一下这个方法
~~~

拿个例子说明一下这个方法

![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYwNDA4MTgzNjUxMTEx?x-oss-process=image/format,png)

**我们可以看出来不断的右移然后或操作，会把1这个位置进行最大化的替换，最后进行一个+1操作，直接得到我们想要的结果**

### 结构设计

> 我们这里讨论的是`JDK1.8`版本以后，因为在这个版本`JDK `对`HashMap`做了很大改动，其中一部分就是结构由原来的`数组加链表`存储改为了`数组加链表和红黑树`,这里我们就要去看看这个(这些数据结构不是我们这里讨论的重点，我们重点在于Map存储结构)
>
> ```java
> /**
>  * The table, initialized on first use, and resized as
>  * necessary. When allocated, length is always a power of two.
>  * (We also tolerate length zero in some operations to allow
>  * bootstrapping mechanics that are currently not needed.)
>  */
> //这就是源码给出的存储数据的地方，没错是一个Node类型的数组，Node是一个实现Map.Entry的内部类，也就是说数组中的数据存储的是一个个节点(是头结点即可，每个头结点又带着一系列数据)，通过哈希算法计算出来存储位置(数组下标)然后将数据存储到这个table中
> transient Node<K,V>[] table;
> ```
>
> **我们来简单看看他的存储方法去体会一下结构的设计**
>
> **红黑树节点**
>
> ~~~java
> static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
>     TreeNode<K,V> parent;  // red-black tree links
>     TreeNode<K,V> left;
>     TreeNode<K,V> right;
>     TreeNode<K,V> prev;    // needed to unlink next upon deletion
>     boolean red;
>     TreeNode(int hash, K key, V val, Node<K,V> next) {
>         super(hash, key, val, next);
>     }
> }
> ~~~
>
> **普通节点**
>
> ~~~java
> static class Node<K,V> implements Map.Entry<K,V> {
>     final int hash;
>     final K key;
>     V value;
>     Node<K,V> next;
> }
> ~~~
>
> 
>
> ```java
> final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
>                boolean evict) {
>     Node<K,V>[] tab; Node<K,V> p; int n, i;
>     if ((tab = table) == null || (n = tab.length) == 0)
>         n = (tab = resize()).length;
>     //使用(n - 1) & hash来计算要插入的数组下标，然后去判断这个位置是否有值，没有则直接放置否则就遍历这个链表，当然也会去计算长度，因为超过树形化阈值要被树形化
>     if ((p = tab[i = (n - 1) & hash]) == null)
>         tab[i] = newNode(hash, key, value, null);
>     else {
>         Node<K,V> e; K k;
>         if (p.hash == hash &&
>             ((k = p.key) == key || (key != null && key.equals(k))))
>             e = p;
>         else if (p instanceof TreeNode)
>             //存储的节点属于红黑树节点则直接插入
>             e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
>         else {
>             for (int binCount = 0; ; ++binCount) {
>                 if ((e = p.next) == null) {
>                     p.next = newNode(hash, key, value, null);
>                     //遍历的时候如果这时候已经超过树形化阈值，那么就会树形化
>                     if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
>                         treeifyBin(tab, hash);
>                     break;
>                 }
> 				//...
>             }
>         }
> 		//...
>     }
> 	//..。
>     return null;
> }
> ```
>
> > 这时候我们可以看到使用`HashMap`存储内容的时候，他会根据给定`key`的`hashCode`这个不变整型算出来一个`hash`值，使用这个作为存储数组的下标，当然取的时候也是根据`key`的`hashCode`来算出索引然后获取值`(first = tab[(n - 1) & hash]) != null)来自源码`，显然这种存储的性能将会更高
> >
> > - 利用了数组的访问方便并减少插入要扩容的性能损耗问题(但是超过大小依旧要扩容，所以可以的话在创建容器的时候我们应该给定容器大小)
> > - 利用了链表删改查方便，但是过长之后就会出现查询较慢(我们总是需要从头结点去遍历)，所以在`JDK1.8`引入了红黑树这个数据结构，在链表过长之后就会转换为红黑树
> > - 利用了红黑树这种特殊的数据结构，它直接将增删改查性能兼并，但是为什么不直接使用红黑树呢，因为这个数据结构本事还是比较复杂，在数据量比较小的时候使用他的并不会对性能有了很好的提升



## 三：尾语

> `HashMap`包含的内容还是比较丰富的，值得我们单独去学习一下，我这里仅仅是冰山一角，很多的还需再去自己揣摩一下