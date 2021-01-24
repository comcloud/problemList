## Java集合

> 谈java集合的话，那就是说我们来看List,Set,Map这个三个容器了，他们并属于java的集合框架，先来张图看看这三个(网上随便搜的一个)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201201192958856.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTY4NjU4Mw==,size_16,color_FFFFFF,t_70)

> 这样看的话感觉有点复杂，毕竟我们使用最频繁的就是
>
> `Map<Object,Object> map = new HashMap<>()`
>
> `List<Object> list = new ArrayList<>()`
>
> `Set<Object> set = new HashSet<>()`

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

