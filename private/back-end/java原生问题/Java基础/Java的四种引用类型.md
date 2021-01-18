# Java的四种引用类型

### 1.概述

Java中四种级别的引用：强引用，软引用，弱引用，虚引用

这四种中，只有强引用FinalReference类是包内可见，其他三种都是public,可以在应用程序中直接使用

### 2.强引用

特点：

1.可以直接访问目标对象

2.所指向的对象在任何时候都不会被系统回收。jvm即使抛出OOM异常，也不会回收强引用所指向的对象，只有在目标堆中没有任何变量指向时，才会被回收。

3.强引用可能导致内存泄漏

### 3.软引用

除强引用外，最强的引用类型，可以通过java.lang.ref.SoftReference使用软引用。一个持有软引用的对象，不会很快的被JVM回收，JVM会根据当前堆的使用情况来判断何时回收。当堆使用率接近阀值时，才会去回收软引用的对象，**<u>因此软引用可以用于实现对内存敏感的高速缓存</u>**

SoftReference的特点是他的一个实例保存对一个Java对象的软引用，该软应用的存在不妨碍垃圾回收线程对该Java对象的回收，也就是一旦SoftReference保存了一个Java对象的软应用，在垃圾线程对这个Java对象回收前，SoftReference类所提供的get方法返回Java对象的强引用。一旦垃圾线程回收Java对象之后，get方法将返回null

例子：在IDE中设置参数：-Xmx2m----规定了堆内存的大小为2m

~~~Java
public void test(){
    MyObject obj = new MyObject();
    SoftReference sf = new SoftReference<>(obj);
    obj = null;
    System.gc();
    //byte[] bytes = new byte[1024*100];
    //System.gc();
    System.out.println("是否被回收" + sf.get());
}
~~~

运行结果：

~~~java
是否被回收MyObject@....
    //结果为MyObject对应的完整包名+一段hashCode
~~~

现在打开注释byte一句，这个时候因为请求堆内存过大，然后又显式调用GC，所有输出结果为null，此时代表软引用被回收

### 4.弱引用

弱引用是一种比软应用较弱的引用类型，在系统GC时，只要发现弱引用（并且不管堆内存空间如何），都会将对象回收。在Java中可以用java.lang.ref.WeakReference实例来保存对一个Java对象的弱引用

~~~java
public void test(){
    MyObject obj = new MyObject();
    WeakReference sf = new WeakReference(obj);
    obj = null;
    System.out.println("是否被回收" + sf.get());
    System.gc();
    System.out.println("是否被回收" + sg.get());
}
~~~

输出结果：第一次有值，显式调用一次GC后，为null

重点：**软引用都非常适合来保存那些可有可无的缓存数据，这样当内存不足时就会被回收，不会导致内存溢出。而当内存充足的时候，这些缓存数据又可以存在相当长的时间，从而起到加速系统的作用**

### 5.虚引用

虚引用为所有类型中最弱的一个。一个持有虚引用的对象，和没有引用几乎一样，随时可能被回收。当试图通过虚引用的get()方法取得强引用时，总是会失败。并且，虚引用必须和引用队列一起使用，他的作用在于追踪垃圾回收过程。

当垃圾回收器准备回收一个对象时，如果发现他还有虚引用，就会在垃圾回收后销毁这个对象，将这个虚引用加入到引用队列。程序可以判断引用队列中是否将要垃圾回收。如果程序发现某个虚引用已经被加入到引用队列，那么就可以在所引用的对象的内存被回收之前采取必要的措施

~~~java
public void test(){
    MyObject obj = new MyObject();
    ReferenceQueue<Object> referenceQueue = new ReferenceQueue<>();
    PhantomReference sf = new PhantomReference<>(obj.referenceaQueue);
    obj = null;
    System.out.println("是否被回收"+sf.get());
    System.gc();
    System.out.println("是否被回收"+sf.get());
    
}
~~~

运行结果都为null

之所以对虚引用的get操作总是null，是因为他的get方法如下

~~~java
public T get(){
    return null;//哈哈哈哈哈哈哈哈
}
~~~

### 6.弱引用典例

WeakHashMap类在Java.util包内，它实现了Map接口，是HashMap的一种实现，他使用弱引用作为内存存储数据的方案。WeakHashMap是弱引用的一种典型应用，他可以作为简单的缓存解决方案。

eg:

~~~java
public void test(){
    Map map;
    map = new WeakHashMap<String ,Object>();
    for(int i = 0 ; i < 10000;i++){
        map.put("key"+i,new byte[i]);
    }
    //map = new HashMap<String,Object>();
    //for(int i = 0 ;i < 10000; i++){
    //map.put("key"+i,new byte[i]);
    //}
}
}
~~~

使用-Xmx2M限定堆内存，使用WeakHashMap代码正常结束，使用HashMap抛出异常：java.lang.OutOfMemoryError:...

所以使用weakHashMap时，在堆内存紧张时，会自动释放掉持有弱引用的内存数据



但是如果WeakHashMap的key都在系统内持有强引用，那么WeakHashMap将会自动退化成普通的HashMap，因为所有的表单项无法被自动清理
