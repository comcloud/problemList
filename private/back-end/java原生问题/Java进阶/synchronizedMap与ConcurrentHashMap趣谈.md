## `synchronizedMap`与`ConcurrentHashMap`闲谈

### 一：区别

> - 都是可以用来解决map同步问题的
> - `synchronizedMap`不仅仅用于`HashMap`，他可以将我们给定的`map`类包装成一个`Collections`中的一个内部类，但是`ConcurrentHashMap`依旧是一个`HashMap`
> - `Collections.synchronizedMap()`和`Hashtable`一样，实现上在调用`map`所有方法时，都对整个`map`进行同步，而`ConcurrentHashMap`的实现却更加精细，它对`map`中的所有桶加了锁

### 二：用法以及介绍

> `synchronizedMap`
>
> ~~~java
> static class SynchronizedList<E> extends SynchronizedCollection<E> implements List<E> {
> //...
> }
> ~~~
>
> ~~~java
> public void synchronizedMapTest(){
>      Map<Integer, String> map = Collections.synchronizedMap(new HashMap<>());
> 				//map等操作，自然执行的方法都是属于collections中的内部类的中的方法
>   			//举例使用put方法
>   			map.put(10,"例子");
> }
> //put源码
> public V put(K key, V value) {
>   //mutex是定义的一个对象，在这里起到全局锁的作用，其实我们这里可以发现，其实最终使用都是传入的map的put方法，也就是Collections中的这些同步修饰类只是加了一层锁作用，但是对于他会把所有的操作都加锁，这对性能有比较大的影响
>   synchronized (mutex) {return m.put(key, value);}
> }
> ~~~
>
> `ConcurrentHashMap`
>
> ~~~java
> ConcurrentHashMap<Integer, String> concurrentHashMap = new ConcurrentHashMap<>();
> concurrentHashMap.put(20,"测试");
> 
> //put源码
> final V putVal(K key, V value, boolean onlyIfAbsent) {
>   if (key == null || value == null) throw new NullPointerException();
>   int hash = spread(key.hashCode());
>   int binCount = 0;
>   for (Node<K,V>[] tab = table;;) {
>     Node<K,V> f; int n, i, fh; K fk; V fv;
>     if (tab == null || (n = tab.length) == 0)
>       tab = initTable();
>     else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
>       if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value)))
>         break;                   // 在添加时候如果这里没有值则不用锁
>     }
>     else if ((fh = f.hash) == MOVED)
>       tab = helpTransfer(tab, f);
>     else if (onlyIfAbsent // check first node without acquiring lock
>              && fh == hash
>              && ((fk = f.key) == key || (fk != null && key.equals(fk)))
>              && (fv = f.val) != null)
>       return fv;
>     else {
>       V oldVal = null;
>       //只有这个地方开始使用锁，也就是说concurrentHashMap对锁的使用更加精细，
>       synchronized (f) {
>        	//...
>       }
>       if (binCount != 0) {
>         //判断是否超过树型化阈值
>         if (binCount >= TREEIFY_THRESHOLD)
>           treeifyBin(tab, i);
>         //判断添加的值是否已经存在
>         if (oldVal != null)
>           return oldVal;
>         break;
>       }
>     }
>   }
>   addCount(1L, binCount);
>   return null;
> }
> 
> ~~~
>
> **当然在`ConcurrentHashMap`中其他位置是没有进行锁操作的，只有在对原表进行增删改的时候才会去判断，而且不是直接对整个代码块加锁，而是优先处理不需要加锁的情况，最后才会加锁，相较于`Collections`中的性能有着更高的实现，但是如果是其他类型的`Map`的话，还是需要去同步仍然需要去使用`Collections`中的这个内部类(这里我们可以去思考一下`HashTable`这个类，为什么这个类已经废弃，如果查看其中的代码的话可以发现这个类几乎是对所有的操作都直接进行加锁操作)**

