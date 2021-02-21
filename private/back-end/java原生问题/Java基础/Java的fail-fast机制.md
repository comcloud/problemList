# Java的fail-fast机制

## 引言

> 开头我们需要谈谈何谓`fail-fast`机制
>
> 在`JDK`中的`ArrayList,HashMap`中可以看到这样一句话：
>
> ~~~java
> /*
> ArrayList
> Note that the fail-fast behavior of an iterator cannot be guaranteed
>  * as it is, generally speaking, impossible to make any hard guarantees in the
>  * presence of unsynchronized concurrent modification.  Fail-fast iterators
>  * throw {@code ConcurrentModificationException} on a best-effort basis.
>  * Therefore, it would be wrong to write a program that depended on this
>  * exception for its correctness: the fail-fast behavior of iterators
>  * should be used only to detect bugs.*/
> /*
> HashMap
> Note that the fail-fast behavior of an iterator cannot be guaranteed
>  * as it is, generally speaking, impossible to make any hard guarantees in the
>  * presence of unsynchronized concurrent modification.  Fail-fast iterators
>  * throw <tt>ConcurrentModificationException</tt> on a best-effort basis.
>  * Therefore, it would be wrong to write a program that depended on this
>  * exception for its correctness: <i>the fail-fast behavior of iterators
>  * should be used only to detect bugs*/
> //他们都反复提到这个机制（快速失败）
> ~~~
>
> ~~~properties
> fail-fast:
> 它是Java集合的一种错误检测机制。当多个线程对集合进行结构上的改变的操作时，有可能会产生fail-fast机制。记住是有可能，而不是一定。例如：假设存在两个线程（线程1、线程2），线程1通过Iterator在遍历集合A中的元素，在某个时候线程2修改了集合A的结构（是结构上面的修改，而不是简单的修改集合元素的内容），那么这个时候程序就会抛出 ConcurrentModificationException 异常，从而产生fail-fast机制。
> ~~~

## Fail-fast示例

~~~java
public class FailFastTest {
    //共有数据集合
    private static List<Integer> list = new ArrayList<>();
    
    private static class threadOne extends Thread{
        public void run() {
            Iterator<Integer> iterator = list.iterator();
            while(iterator.hasNext()){
                int i = iterator.next();
                System.out.println("ThreadOne 遍历:" + i);
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    
    private static class threadTwo extends Thread{
        public void run(){
            int i = 0 ; 
            while(i < 6){
                System.out.println("ThreadTwo run：" + i);
                if(i == 3){
                    list.remove(i);
                }
                i++;
            }
        }
    }
   
    public static void main(String[] args) {
        for(int i = 0 ; i < 10;i++){
            list.add(i);
        }
        new threadOne().start();
        new threadTwo().start();
    }
}
~~~

> 出现以下结果`ConcurrentModificationException`

~~~bash
ThreadOne 遍历:0
ThreadTwo run：0
ThreadTwo run：1
ThreadTwo run：2
ThreadTwo run：3
ThreadTwo run：4
ThreadTwo run：5
Exception in thread "Thread-0" java.util.ConcurrentModificationException
    at java.util.ArrayList$Itr.checkForComodification(Unknown Source)
    at java.util.ArrayList$Itr.next(Unknown Source)
    at test.ArrayListTest$threadOne.run(ArrayListTest.java:23)
~~~

## 原因

> 我们在对一个Collection进行迭代的同时也对这个集合进行修改，这时候迭代器就会抛出`ConcurrentModificationException`，也就是快速失败

**来看看源码**

~~~java
//ArrayList集合内部实现的迭代器
private class Itr implements Iterator<E> {
    int cursor;
    int lastRet = -1;
    int expectedModCount = modCount;

    public boolean hasNext() {
        return (this.cursor != ArrayList.this.size);
    }
    public E next() {
        checkForComodification();
        /** 省略此处代码 */
    }
    public void remove() {
        if (this.lastRet < 0)
            throw new IllegalStateException();
        checkForComodification();
        /** 省略此处代码 */
    }
    final void checkForComodification() {
        if (modCount != expectedModCount)
            //抛出异常，注意这里的条件就是关键的地方
            throw new ConcurrentModificationException();
    }
}
~~~

> 上面源码中在一种情况下就会抛出这个异常，那就是`modCount`()与`expectedModCount`
>
> `modCount`是外部对ArrayList数据修改的时候会进行变化
>
> `expectedModCount`是Itr内部定义的一个变量，也就是取决于`modCount`
>
> 所以上面报出这个错误的原因就是：当一个线程修改了数据，然后另一个线程访问的时候发现这两个值不等，所以就会抛出这个异常，也就是说`expectedModCount`是迭代器内部变量来监测外部是否有变化的，当然他们会尽力去找到这个异常然后抛出来。

## 解决办法

> 我们这里给出一些解决方案

~~~java
/**
方案一：在遍历过程中所有涉及到改变modCount值得地方全部加上synchronized或者直接使用		  Collections.synchronizedList，这样就可以解决。但是不推荐，因为增删造成的同步锁可能会阻塞遍历操作。

方案二：使用CopyOnWriteArrayList来替换ArrayList。推荐使用该方案。
CopyOnWriterArrayList所代表的核心概念就是：任何对array在结构上有所改变的操作（add、remove、clear等），CopyOnWriterArrayList都会copy现有的数据，再在copy的数据上修改，这样就不会影响COWIterator中的数据了，修改完成之后改变原有数据的引用即可。同时这样造成的代价就是产生大量的对象，同时数组的copy也是相当有损耗的。
*/
~~~

