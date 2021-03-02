>  **为什么java序列化要实现`Serializable`接口**

### 一：序列化与反序列化

- 序列化：把对象转换为字节序列的过程称为对象的序列化.
- 反序列化：把字节序列恢复为对象的过程称为对象的反序列化.

### 二：使用场景

> 如果创建的对象只需要在`JVM`中存在，那么是用不着序列化的，这个对象一直存在到内存中，但是如果我们想要可以看到对象，就需要序列化出来，或许打印或保存到磁盘。同时这里我们需要想一下，我们在做前后端交互时候，后台做过处理的数据实际上此时是在内存中的，但是我们前台拿到的可以是一个`JSON`串，其实其中还是有一个序列化过程。
>
> 这里看`String`的部分源码
>
> ~~~java
> //实现了Serializable接口，并且显式指定了serialVersionUID值，这个是作为序列化与反序列化的钥匙
> public final class String
>     implements java.io.Serializable, Comparable<String>, CharSequence,
>                Constable, ConstantDesc {
>                  @java.io.Serial
>                    private static final long serialVersionUID = -6849794470754667710L;
>                  //...
>                }
> ~~~
>
> 
>
> 总之一句话：如果我们需要对对象进行持久化或者网络传输，就需要序列化

### 三：为什么要实现这个接口

如果我们实现了这个接口，那么JVM会在底层帮我们进行序列化与反序列化，当然不实现这个接口也是可以的，不过具体办法这里不多做介绍，不是我们的重点

### 四：serialVersionUID作用

如果不显示指定 serialVersionUID， JVM 在序列化时会根据属性自动生成一个 serialVersionUID， 然后与属性一起序列化，再进行持久化或网络传输。在反序列化时，JVM 会再根据属性自动生成一个新版 serialVersionUID，然后将这个新版 serialVersionUID 与序列化时生成的旧版 serialVersionUID 进行比较，如果相同则反序列化成功， 否则报错.

这里我们自己写个代码来验证一下

**首先一个实体类**

~~~java
@Data
public class Person implements Serializable {
    private int age;
    private String name;
    private List<Person> child;
}
~~~

```java
private void serialize(Person person) throws Exception {
    ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(new File("/Users/result.txt")));
    oos.writeObject(person);
    oos.close();
}

private Person deserialize() throws Exception {
    ObjectInputStream ois = new ObjectInputStream(new FileInputStream(new File("/Users/result.txt")));
    return (Person) ois.readObject();
}

@Test
public void serTest() throws Exception {

    Person person = Person.builder().age(10).name("zhangsan").build();
    System.out.println("序列化前的结果: " + person);

    serialize(person);

    Person dPerson = deserialize();
    System.out.println("反序列化后的结果: " + dPerson);
}
```

此时肯定是没有问题的，但是我们现在进行以下操作

> 1.先注释掉反序列化代码， 执行序列化代码
>
> 2.然后 User 类新增一个属性 sex(之所以修改这个类，是因为在开发时候难以避免代码是不变的，以此来凸显UID的作用)
>
> 3.注释掉序列化代码，执行反序列化代码

此时会出现以下错误

> java.io.InvalidClassException: com.cloud.MainTest.bean.Person; local class incompatible: stream classdesc serialVersionUID = -7144725520034302847, local class serialVersionUID = 4839988237181502069
>
> 此时说明在我们序列化的时候产生的UID与反序列化时候用的UID是不同的，但是我们在`Person`类中指定一个UID，那么就不会有这个错误

### 五：序列化的其他知识

**被 transient 关键字修饰的属性不会被序列化， static 属性也不会被序列化.（这里被static修饰的不被序列化应该懂得，那就是序列化是对于对象而言的，但是static是优先于对象存在的）**

> 这个知识是我们应该知道的，同时我们需要知道一点就是关于UID他也是`static`，所以他并没有被序列化，而是JVM在生成UID之后会把我们给定的UID进行重新赋值