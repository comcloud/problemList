# 深入理解java枚举

> enum关键字在 java5 中引入，表示一种特殊类型的类，其总是继承java.lang.Enum类，更多内容查看[官方文档](https://docs.oracle.com/javase/6/docs/api/java/lang/Enum.html)。

## 一：引言

> 我们使用枚举类大多数时候都是跟常量作对比，因为很多时候我们都是使用枚举来替换常量的，优势显而易见：**以这种方式定义的常量使码更具可读性，允许进行编译时检查，预先记录可接受值的列表，并避免由于传入无效值而引起的意外行为**

~~~java
public enum OrderStatus {
	PAY,
    NOT_PAY,
   	END
}
//以上就是一个枚举的简单例子，表示一个订单的状态，我们只需要用他来作为一个状态而已没有其他额外的意思
~~~

~~~java
System.out.println(OrderStatus.END.name());
System.out.println(OrderStatus.END.name().getClass());
System.out.println(OrderStatus.END);
System.out.println(OrderStatus.END.getClass());
/**
END
class java.lang.String
END
class com.cloud.bean.OrderStatus
*/
~~~

