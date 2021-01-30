# 聊一聊BigDecimal

> 一段简单的代码 

```java
System.out.println(0.05 + 0.01);  
System.out.println(1.0 - 0.42);
System.out.println(4.015 * 100);  
System.out.println(123.3 / 100);
/* 
输出：
	0.060000000000000005
	0.5800000000000001
	401.49999999999994
	1.2329999999999999
*/
```

​      由此可见我们在`java` 中使用浮点数计算的时候就会出现精度丢失的问题。	

> ## 精度损失的原因

我们以为的算术运算和计算机计算的并不完全一致。

这是因为计算机是以二进制存储数值的，我们输入的十进制数值都会转换成二进制进行计算，十进制转二进制再转换成十进制就不是原来那个十进制了。

举个例子：十进制的0.1转换成二进制是0.0 0011 0011 0011...（无数个0011），再转换成十进制就是0.1000000000000000055511151231。

计算机无法精确地表达浮点数，这是不可避免的，也是为什么浮点数计算后精度损失的原因。

>  举一个例子

​		我去一个商店买一个5毛的冰淇淋，和一个1毛钱的辣条 我只有6毛钱

```java
if(0.6>0.1+0.5){
    sout("支付成功！");
}else{
	sout("余额不足");
}
```

在大型商城中出现这种问题会发生很严重的后果，下单失败，6毛钱收了 6.000001毛。我们基本已经形成了常识，需要用到金钱的地方要用BigDecimal而不是其他 所以接下来我们 就可以使用Java中的BigDecimal类来解决这类问题

> ## BigDecimal 常用的API

```java
构造器                   描述
BigDecimal(int)       创建一个具有参数所指定整数值的对象。
BigDecimal(double)    创建一个具有参数所指定双精度值的对象。
BigDecimal(long)      创建一个具有参数所指定长整数值的对象。
BigDecimal(String)    创建一个具有参数所指定以字符串表示的数值的对象。
方法
方法                    描述
add(BigDecimal)       BigDecimal对象中的值相加，然后返回这个对象。
subtract(BigDecimal)  BigDecimal对象中的值相减，然后返回这个对象。
multiply(BigDecimal)  BigDecimal对象中的值相乘，然后返回这个对象。
divide(BigDecimal)    BigDecimal对象中的值相除，然后返回这个对象。
toString()            将BigDecimal对象的数值转换成字符串。
doubleValue()         将BigDecimal对象中的值以双精度数返回。
floatValue()          将BigDecimal对象中的值以单精度数返回。
longValue()           将BigDecimal对象中的值以长整数返回。
intValue()            将BigDecimal对象中的值以整数返回。
```

> ## BigDecimal 精度一定不会丢是吗？

```java
BigDecimal a = new BigDecimal(1.01);
BigDecimal b = new BigDecimal(1.02);
BigDecimal c = new BigDecimal("1.01");
BigDecimal d = new BigDecimal("1.02");
System.out.println(a.add(b));
System.out.println(c.add(d));
/*
输出 >:
    2.0300000000000000266453525910037569701671600341796875
	2.03
*/	
```

可见论丢失精度BigDecimal显的更为过分。但是使用Bigdecimal的BigDecimal(String)构造器的变量在进行运算的时候却没有出现这种问题。

从源码的注释中官方也给出了说明，如下是BigDecimal类的double类型参数的构造器上的注释说明：

```java
 /**
     * Translates a {@code double} into a {@code BigDecimal} which
     * is the exact decimal representation of the {@code double}'s
     * binary floating-point value.  The scale of the returned
     * {@code BigDecimal} is the smallest value such that
     * <code>(10<sup>scale</sup> &times; val)</code> is an integer.
     * <p>
     * <b>Notes:</b>
     * <ol>
     * <li>
     
     * The results of this constructor can be somewhat unpredictable.
     * One might assume that writing {@code new BigDecimal(0.1)} in
     * Java creates a {@code BigDecimal} which is exactly equal to
     * 0.1 (an unscaled value of 1, with a scale of 1), but it is
     * actually equal to
     * 0.1000000000000000055511151231257827021181583404541015625.
     * This is because 0.1 cannot be represented exactly as a
     * {@code double} (or, for that matter, as a binary fraction of
     * any finite length).  Thus, the value that is being passed
     * <em>in</em> to the constructor is not exactly equal to 0.1,
     * appearances notwithstanding.
     *
     * <li>
     * The {@code String} constructor, on the other hand, is
     * perfectly predictable: writing {@code new BigDecimal("0.1")}
     * creates a {@code BigDecimal} which is <em>exactly</em> equal to
     * 0.1, as one would expect.  Therefore, it is generally
     * recommended that the {@linkplain #BigDecimal(String)
     * String constructor} be used in preference to this one.
     *
     
     * <li>
     * When a {@code double} must be used as a source for a
     * {@code BigDecimal}, note that this constructor provides an
     * exact conversion; it does not give the same result as
     * converting the {@code double} to a {@code String} using the
     * {@link Double#toString(double)} method and then using the
     * {@link #BigDecimal(String)} constructor.  To get that result,
     * use the {@code static} {@link #valueOf(double)} method.
     * </ol>
     *
     * @param val {@code double} value to be converted to
     *        {@code BigDecimal}.
     * @throws NumberFormatException if {@code val} is infinite or NaN.
     */
    public BigDecimal(double val) {
        this(val,MathContext.UNLIMITED);
    }
```

第三段也说的很清楚它只能计算的无限接近这个数，但是无法精确到这个数。

第四段则说，如果要想准确计算这个值，那么需要把double类型的参数转化为String类型的。并且使用BigDecimal(String)这个构造方法进行构造。去获取结果。



> ## 浮点数的舍入和格式化该如何选择？

- 我们首先来看看使用String.format的格式化舍入，会有什么结果，我们知道浮点数有double和float两种，下边我们就用这两种来举例子：

```java
double num1 = 3.35;
float num2 = 3.35f;
System.out.println(String.format("%.1f", num1));
System.out.println(String.format("%.1f", num2))
    
/*
输出结果
3.4
3.3
*/
```

结果又是很懵逼

得到的结果似乎与我们的预期有出入，其实这个问题也很好解释，double和float的精度是不同的，double的3.35相当于3.350000000000000088817841970012523233890533447265625，而float的3.35相当于3.349999904632568359375，String.format才有的又是四舍五入的方式舍入，所以精度问题和舍入方式就导致了运算结果与我们预期不同。

Formatter类中默认使用的是HALF_UP的舍入方式，如果我们需要使用其他的舍入方式来格式化，可以手动设置。

到这里我们就知道通过String.format的方式来格式化这条路坑有点多，所以，**「浮点数的字符串格式化还得要使用BigDecimal来进行」**。

来，上代码!!!

```java
BigDecimal num1 = new BigDecimal("3.35");
//小数点后1位，向下舍入
BigDecimal num2 = num1.setScale(1, BigDecimal.ROUND_DOWN);
System.out.println(num2);
//小数点后1位，四舍五入
BigDecimal num3 = num1.setScale(1, BigDecimal.ROUND_HALF_UP);
System.out.println(num3);
输入结果：
3.3
3.4
这次得到的结果与我们预期一致。
```

> ## BigDecimal能否使用equals方法比较？

我们都知道，包装类的比较要使用equals，而不能使用==，那么这一条在Bigdecimal中也适用吗？数据说话，上代码：

```java
System.out.println(new BigDecimal("1").equals(new BigDecimal("1.0")))
结果：false
```

按照我们的理解1和1.0是相等的，也应该是相等的，但是Bigdecimal的equals在比较中不只是比较了value，还比较了scale，我们前边说了scale是小数点后的位数，明显两个值的小数点后位数不一样，所以结果为false。

实际的使用中，我们常常是只希望比较两个BigDecimal的value，这里就要注意，要使用compareTo方法：

```java
System.out.println(new BigDecimal("1").compareTo(new BigDecimal("1.0")))
结果：true
```

# 总结

- 避免使用Double来进行运算
- BigDecimal的初始化要使用String为参数的
- 浮点数的格式化建议使用BigDecimal
- 比较两个BigDecimal的value要使用compareTo