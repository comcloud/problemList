## CAS原理

### 1.介绍

> `CAS`指的就是`compare and set/swap/exchange`，总之是一种先比较然后才回去设置/更改值的方法，根据一个简单例子来看看
>
> ~~~java
> private static int number = 0;
> 
> //这个总是会低于我们的期望值是因为一个线程在改变时候，其他线程也可能进行了改变，倘若某个时刻A线程操作number时候是8，累加一下变为9然后换到其他线程，但是这个时候内存中的number已经不是8，而是已经被其他线程改为了9甚至其他的数字(为什么会被其他线程改变呢，首先进行一个自增操作不是一个原子性操作，从内存取number的值拿到寄存器然后再被ALU进行运算，在返回写入内存，这些过程会存在其他线程进来)，那么就会出现这个结果总是低于期望值的情况
> public void testCASByNothing() throws InterruptedException {
>   CountDownLatch latch = new CountDownLatch(10);
>   Thread[] threads = new Thread[10];
>   //这里我们去开启10个线程，然后每个线程分别给number变量累加10000，最后的结果应该是10 0000
>   for(int i = 0 ; i < 10; i++){
>     threads[i] = new Thread(() -> {
>       for(int j = 0; j < 10000; j++){
>         number++;
>       }
>       latch.countDown();
>     });
>   }
>   Arrays.stream(threads).forEach(Thread::start);
> 
>   latch.await();
>   System.out.println("无处理:    " + number);
> 
> }
> 
> //这个使用synchronized来直接同步，但是这个性能并不是那么好
> public void testCASBySynchronized() throws InterruptedException {
>   CountDownLatch latch = new CountDownLatch(10);
>   Thread[] threads = new Thread[10];
>   Object o = new Object();
>   //这里我们去开启10个线程，然后每个线程分别给number变量累加10000，最后的结果应该是10 0000
>   for(int i = 0 ; i < 10; i++){
>     threads[i] = new Thread(() -> {
>       synchronized (o) {
>         for (int j = 0; j < 10000; j++) {
>           number++;
>         }
> 
>         latch.countDown();
>       }
>     });
>   }
>   Arrays.stream(threads).forEach(Thread::start);
> 
>   latch.await();
>   System.out.println("添加synchronized锁:    " + number);
> 
> }
> 
> //原子类
> private AtomicInteger count = new AtomicInteger(0);
> 
> @Test
> public void testCASByAtomic() throws InterruptedException {
>   CountDownLatch latch = new CountDownLatch(10);
>   Thread[] threads = new Thread[10];
>   //这里我们去开启10个线程，然后每个线程分别给number变量累加10000，最后的结果应该是10 0000
>   for (int i = 0; i < 10; i++) {
>     threads[i] = new Thread(() -> {
>       for (int j = 0; j < 10000; j++) {
>         count.incrementAndGet();
>       }
> 
>       latch.countDown();
>     });
>   }
>   Arrays.stream(threads).forEach(Thread::start);
> 
>   latch.await();
>   System.out.println("使用原子类:    " + count);
> 
> }
> 
> ~~~
>
> > 参考上面的例子，上述代码中通过三种来对一个变量进行多线程累加，第一个直接进行累加，但是总是会低于我们期望的值，第二种和第三种满足，使用`synchroized`是直接将线程同步，但是不是体现我们说的内容，我们要说的就是第三种使用原子类为什么可以
> >
> > **这个就体现出`CAS`的作用了，他是一种先比较然后才会去判断是否重新设置值，看下出现这个情况的原因，A线程对数字进行改变，但是写入到内存时候已经不是原来的数字，这个就是一个比较，也就比较是否是原来的数字，如果是的话则会进行set，否则就会拿到现在这个值重新去CPU进行计算再写回来判断，依次反复，也就是自旋操作**

### 2.优缺点分析

1.CPU开销较大
在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，循环往复，会给CPU带来很大的压力。

2.不能保证代码块的原子性
CAS机制所保证的只是一个变量的原子性操作，而不能保证整个代码块的原子性。比如需要保证3个变量共同进行原子性的更新，就不得不使用`Synchronized`了。

> 其实这个地方也是在说轻量级锁与重量级锁的比较，因为轻量级不一定比重量级轻量
>
> `CAS`不断重试，的确不存在线程的选择切换，但是如果线程数比较多的话会给CPU带来很大压力，而`synchronized`则可以直接将一块区域同步，其他线程没有拿到锁就进入`BLOCKED`状态

### 3.代码介绍

> 这里我们仍然使用最上面的例子来看`CAS`
>
> ~~~java
> //这个是使用原子类的自增方法，他直接使用unsafe类的自增方法
> public final int incrementAndGet() {
>   return U.getAndAddInt(this, VALUE, 1) + 1;
> }
> //这个是unsafe中的方法，其中weakCompareAndSetInt就是我们所说的CAS机制，当然这个方法是本地方法需要特殊的方法才可以看到，不过这里暂时不介绍，我们只需要知道这个时候就是在走的这个设置机制原理
> public final int getAndAddInt(Object o, long offset, int delta) {
>   int v;
>   do {
>     v = getIntVolatile(o, offset);
>   } while (!weakCompareAndSetInt(o, offset, v, v + delta));
>   return v;
> }
> public final boolean weakCompareAndSetInt(Object o, long offset,
>                                           int expected,
>                                           int x) {
>   //CAS
>   return compareAndSetInt(o, offset, expected, x);
> }
> //native方法
> public final native boolean compareAndSetInt(Object o, long offset,
>                                              int expected,
>                                              int x);
> ~~~
>
> 
>
> 