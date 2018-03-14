// 1.`public final native Class<?>`

getClass\(\),效果与Object.class一样

一般联合getName\(\)使用，返回当前对象的运行时类 如：com.me.demo.Person



// 2.`public native int hashCode()`

返回当前对象的哈希值

重写equals方法时，必须重写hashCode方法，这是因为hashCode具有如下约定：

1.在Java应用程序执行期间，对于同一个对象多次调用hashCode\(\)方法时，其返回的哈希码是相同的，前提是没有修改equals\(\)方法

2.如果两个对象相等，那么这两个对象hashCode\(\)方法返回的哈希码必须相等

3.两个对象hashCode\(\)返回的哈希码相等，这两个对象不一定相等

至于为什么要重写hashCode\(\)方法，参见：

[https://zhuanlan.zhihu.com/p/29511703](https://zhuanlan.zhihu.com/p/29511703)



// 3.

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

Object原生的equals方法内部使用的是"=="，所以一般来讲，判断两个对象是否相等，需要重写equals方法

假设Person类的属性有uid、name、age，重写Person类的equals方法:

```java
public boolean equals(Object obj) {
    if(obj == null || !(obj instanceof Person) {
        return false;
    }
    Person p = (Person)obj;
    if(p.getUid() == this.getUid()) {
        return true;
    }
    return false;
}
```



// 4.`protected native Object clone()`

克隆

clone\(\)方法是一个被native修饰的方法，因为它并不是java原生的方法，具体的实现是由C/C++完成的。

clone因为翻译为“克隆”，其目的是创建并返回此对象的一个副本。

```java
// 假设Person已经实现了Cloneable接口
Person p1 = new Person();
p1.setName("zhangsan");
p1.setAge(20);
Person p2 = p1;
Person p3 = (Person)p1.clone();
p3.setName("lisi");
// 到此为止,p1和p2的name属性值为"zhangsan",p3的name属性值为"lisi"
```



上述代码中，p1和p2指向同一块内存的某个对象，p3是p1克隆出来的，也就是说p3和p1指向不同的对象，但是p1和p3的属性值和方法是一样的，此时修改p3的属性值对p1没有影响

但是，假设Person有一个Date类型\(除了基本数据类型和String类型\)的属性birthday，那么p3的birthday属性和p1的birthday属性就指向了同一个Date对象。

此时执行p3.setBirthday\(...\)，会对p1的birthday属性造成影响吗？答案是不会，这里只是将p3的birthday的属性指向了一个新的对象，而p1的birthday属性指向不变

但是如果执行p3.getBirthday\(\).setTime\(...\)，就会对p1的birthday属性造成影响。因为p3.getBirthday\(\)获取了一个Date对象，而p1的birthday属性也指向这个Date对象。

此时需要重写clone方法:

```java
protected Object clone() throws CloneNotSupportedException {
    Person p = (Person)super.clone();
    p.birthday = (Date)birthday.clone();
    return p;
}
```



// 5.`public String toString()`

返回对象的字符串表现形式



// 6.多线程相关

notify\(\)

notifyAll\(\)

wait\(long\)/wait\(long, int\)/wait\(\)

参见：

[https://zhuanlan.zhihu.com/p/29511703](https://zhuanlan.zhihu.com/p/29511703)



// 7.`finalize()`

JVM准备对此对象所占的内存空间进行回收之前，该方法将被调用

此方法不需要我们主动调用

