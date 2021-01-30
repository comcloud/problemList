## Java集合

> 谈java集合的话，那就是说我们来看List,Set,Map这个三个容器了，他们并属于java的集合框架，先来张图看看这三个(网上随便搜的一个)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201201192958856.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTY4NjU4Mw==,size_16,color_FFFFFF,t_70)

> 真正说之前我们需要思考一个问题：**为什么要使用集合**，虽然这些集合框架很丰富，但是我们为什么要使用呢？这个就要回归到集合的本身作用，集合实际上就是一个容器，容器的作用就是存放东西，存放 东西有个最基本的数据结构**数组**,数组的缺点显而易见(大小固定，存储数据有序而且可以重复，使用起来也很单一)，所以就有了集合的存在，他提高了数据存储的灵活性(即使底层使用数组来实现一部分)



### 集合简单理解

- 这里我必须要先说明java集合这个东西是怎么样的一个概念

> - 可迭代，我们要知道一个集合必须是可以迭代的，这个迭代不仅仅是遍历这么简单，我们还必须可以对集合中的元素可以按照某种规范的访问，java设计出了`Iterable`,这个单词意思就是可迭代的(源码中对这个接口解释是：实现这个接口让你的目标可以进行一个`"for-each loop"`，所以也就诞生了集合的下个接口:`Collection`(收集的意思))
> - 可储存，上面也提到集合不仅仅局限于`loop`，而且可存储，这时候我们就可以知道如果我们存储第一个东西那么就是我们拿到了对这个目标对象的处理权(官方给集合的定义就是：存储一组对象的容器)
> - 多样性，对于不同的场景我们需要不同的容器，这种思想不仅仅适用于java集合，也适用很多情况，否则也不会有着那么多看似一样的东西，java集合这里根据不同的引用场景给`Collection`实现了三种不同类型的集合，当然也依旧是接口(接口就是一种规范)，分别有:`List,Set,Map`，特殊一点的是Map不是实现`Collection`的接口，而是JDK对于键值对这种特殊存储方式给出的容器，不过我们依旧把他划分为java集合框架中
> - 多实现，当我们根据不同的大场景下划分出了三种类型容器，可是我们同时也要考虑一件事情，平常的业务等操作不仅仅我们给出那么少，为了弥补这一点，JDK又给划分了：`List:ArrayList(是单独的的一个类，不是Arrays的内部类),LinkedList；Set:HashSet,TreeSet,SortedSet,Map:HashMap,LinkedHashMap,TreeMap,SortedMap,HashTable(跟HashMap类似，不过HashMap有更高效的实现，所以这个废弃了)`

### 单个聊

#### 1.List与Set

> 这俩集合很相似，都是属于单列元素的集合，不过正如我们上面说的都是为了不同的业务场景，所以这里谈谈这俩的区别
>
> - Set : A collection that contains no duplicate elements
>
> 这个是源码一上去就提到的一句话，就是说不允许包含重复元素，这个重复元素我们要知道就是两个对象是否equals相等，相等就不让插入，add方法就会返回false;这个集合容器获取元素时候，只可以根据Iterator接口获取所有元素来获取元素
>
> - List : An ordered collection (also known as a <i>sequence</i>)
>
> 一个有先后顺序的集合，怎么去理解这个先后顺序呢，我们来去想一下ArrayList与LinkedList（一个数组实现，一个链表实现），现在是不是懂一点先后顺序了，当然也可以插队存储。我们来获取其中的值时候可以按照Iteator接口获取所有的元素，也可以直接根据get(index)来获取指定对象
>
> **总结** : List 以特定次序来持有元素，可有重复元素。Set 无法拥有重复元素,内部排序。



#### 2.Map

> 一个映射键值对的容器，一个map中不允许有重复的key，一个key最多可以映射到一个值(最多映射一个值，同时也可以不映射值，也就是为null，但是实际上不同的Map实现对key与value的约束不同)
>
> 平时我们使用这个map时候最多的就是HashMap（JDK8对他性能进行了很大提升，他的亮点一是默认值，二是结构设计，当然对于Map的很多实现不是我们这里讨论的地方）
>
> Map中有个特殊的结构就是Entry，他是一个单个存储键值对的一个类，如果我们吧一个Map中的数据都想象成一个个节点，每个节点都是由单个键值对组成，那么每个节点就是一个Entry实例化对象
>
> ```java
> Set<Map.Entry<K, V>>
> ```
>
> 这个在Map中算是一个比较有魔法的地方，不过我们需要单独列出一个文章来讨论，这里不再细讲

- AbstractMap

~~~java
//类似于List是由AbstractList实现，Set是由AbstractSet实现
//这里我们需要理解三点：接口类，抽象类，普通类(可以有可实例化与不可实例化)，为什么接口我们知道了，他是一种规范，或者说一种代码式的东西，我们为了方便后续实现或者期望实现，普通类不用说，那么抽象类呢
/**
抽象类：
抽象类是用来捕捉子类的通用特性的，是被用来创建继承层级里子类的模板。现实中有些父类中的方法确实没有必要写，因为各个子类中的这个方法肯定会有不同；而写成抽象类，这样看代码时，就知道这是抽象方法，而知道这个方法是在子类中实现的，所以有提示作用
*/
//这个抽象Map中有个需要注意的地方就是toString方法，懂这个对于我们理解entrySet有很好的帮助
~~~

### 怎么选用集合

> 我们可以根据集合的特点加上我们自己的业务需求来确定
>
> 比如我们需要根据键值获取到元素值时就选用 `Map` 接口下的集合，需要排序时选择 `TreeMap`,不需要排序时就选择 `HashMap`,需要保证线程安全就选用 `ConcurrentHashMap`。
>
> 当我们只需要存放元素值时，就选择实现`Collection` 接口的集合，需要保证元素唯一时选择实现 `Set` 接口的集合比如 `TreeSet` 或 `HashSet`，不需要就选择实现 `List` 接口的比如 `ArrayList` 或 `LinkedList`，然后再根据实现这些接口的集合的特点来选用。



### 谈一下过时的集合

> 虽然集合中有些东西过时，但是还是要谈一下的，我们要做到不仅知其然还是所以然

- `Vector`

~~~java
/**
这个对于我们来说算是比较陌生的了，他实际上线程安全的，ArrayList是线程不安全的，但是为什么会弃用呢？
Vector同步每个单独的操作。这几乎从来不是你想要做的。
通常你想同步整个操作序列。同步单个操作不太安全（Vector例如，如果你遍历一个，例如，你仍然需要取出一个锁，以避免其他人同时改变集合，这将导致ConcurrentModificationException迭代线程），但也更慢为什么要重复一次锁就好了）？
当然，即使你不需要，它也有锁定的开销。
基本上，在大多数情况下，这是一个非常有缺陷的同步方法。正如Brian Henk先生指出的那样，您可以使用如下调用来装饰集合Collections.synchronizedList：Vector将“调整大小的数组”集合实现与“每个操作同步”位相结合的事实是设计不佳的另一个例子; 装饰方式让人更清楚的分离关注点。
*/
//比如参见以下源码，同步整个操作序列
public synchronized void removeElementAt(int index) {
    modCount++;
    if (index >= elementCount) {
        throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                 elementCount);
    }
    else if (index < 0) {
        throw new ArrayIndexOutOfBoundsException(index);
    }
    int j = elementCount - index - 1;
    if (j > 0) {
        System.arraycopy(elementData, index + 1, elementData, index, j);
    }
    elementCount--;
    elementData[elementCount] = null; /* to let gc do its work */
}
~~~

> 既然这样的话，Vector是线程安全的但是被弃用了(或者说是不推荐使用)，那么在多线程情况下我们应该使用什么集合容器呢，我这里说几种

1. `java.util.Collections.SynchronizedList`

~~~java
//传入一个集合包装成一个线程安全的集合，这时候HashMap也有同样类似的实现
//但是这个同步集合性能不是挺好的，内部所有方法都是带有同步对象锁的
SynchronizedList(List<E> list) {
    super(list);
    this.list = list;
}
~~~

2. `java.util.concurrent.CopyOnWriteArrayList
   java.util.concurrent.CopyOnWriteArraySet`

~~~java
//这两个类属于并发包下的，CopyOnWrite（简称：COW）：即复制再写入，就是在添加元素的时候，先把原 List 列表复制一份，再添加新的元素。
//这个属于List中的add源码，
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    //加锁
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        //重新复制一份
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        //把新的设置进去
        setArray(newElements);
        return true;
    } finally {
        //释放锁
        lock.unlock();
    }
}
//但是获取的话就不用加锁
private E get(Object[] a, int index) {
    return (E) a[index];
}
//Set那个底层直接使用List来实现了，不过我们还是要知道Set的一个特点那就是不重复，那么他会去判断添加的元素是否已经存在
~~~

> 这两种并发集合，虽然停厉害，但只适合于读多写少的情况，如果写多读少，使用这个就没意义了，因为每次写操作都要进行集合内存复制，性能开销很大，如果集合较大，很容易造成内存溢出。



- `Hashtable`

> 这个也被弃用，弃用原因主要因为`HashMap`有了更高效的实现，我们大多数时候使用`HashMap`来就可以，但是`Hashtable`是线程安全的，类似于上面的Vector，`HashMap`是线程不安全的，同时我们可以进行包装或者直接创建一个线程安全的
>
> `Collections.synchronizedMap()`
>
> `ConcurrentHashMap`

> 说个题外话，为什么`JDK`中有`Hashtable`这个不满足命名规范的东西，一句话：他是很古老的一个实现类
>
> `Hashtable` 是在 Java 1.0 的时候创建的，而集合的统一规范命名是在后来的 Java 2 开始约定的，当时其他一部分集合类的发布构成了新的集合框架。
>
> 顺便说一下，这样就使得 `Hashtable` 过时了，所以不应该在新代码中继续使用它。

### 提一嘴

> 这个集合都是使用的泛型，这些泛型并非是随便写的，虽然没有规定，但是也是一个习惯：
>
> E : Element ( 元素 )
>
> K : Key（键）
>
> V : Value（值）
>
> T : Type（类型）

