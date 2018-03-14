9种基本数据类型，以及他们的封装类

| 数据类型 | 字节 | 位 | 取值范围 | 封装类 |
| :--- | :--- | :--- | :--- | :--- |
| byte | 1 | 8 | -2^7 ~ 2^7-1 | Byte |
| short | 2 | 16 | -2^15 ~ 2^15-1 | Short |
| int | 4 | 32 | -2^31 ~ 2^31-1 | Integer |
| long | 8 | 64 | -2^63 ~ 2^63-1 | Long |
| float | 4 | 32 | 精确到小数点后6位 | Float |
| double | 8 | 64 | 精确到小数点后15位 | Double |
| char | 2 | 16 | \u0000 ~ \uffff | Character |
| boolean |  |  |  | Boolean |

* 浮点数默认类型为double
* char类型是单一的十六位Unicode字符
* float和double的精度问题，参考：[https://www.zhihu.com/question/46432979/answer/221485161](https://www.zhihu.com/question/46432979/answer/221485161)

**问题一：**

```
Integer a = 1000;
Integer b = 1000;

Integer c = 100;
Integer d = 100;

System.out.println(a == b);// false
System.out.println(c == d);// true
```

为什么 1000 == 1000 结果为false，而 100 == 100 结果为true?

Integer.java 类，你会发现有一个内部私有类，IntegerCache.java，它缓存了从-128到127之间的所有的整数对象。所以事情就成了，所有的小整数在内部缓存，然后当我们声明类似 Integer c = 100; 的时候，它实际上在内部做的是 Integer i = Integer.valueOf\(100\);现在，如果我们去看valueOf\(\)方法，我们可以看到

```java
public static Integer valueOf(int i) {
  assert IntegerCache.high >= 127;
  if (i >= IntegerCache.low && i <= IntegerCache.high)
       return IntegerCache.cache[i + (-IntegerCache.low)];
  return new Integer(i);
}
```

如果值的范围在-128到127之间，它就从高速缓存返回实例。所以 Integer c = 100, d = 100;指向了同一个对象。这就是为什么我们写System.out.println\(c == d\);我们可以得到true。现在你可能会问，为什么这里需要缓存？合乎逻辑的理由是，在此范围内的“小”整数使用率比大整数要高，因此，使用相同的底层对象是有价值的，可以减少潜在的内存占用。摘自：[https://zhuanlan.zhihu.com/p/32774770?utm\_source=qq&utm\_medium=social](https://zhuanlan.zhihu.com/p/32774770?utm_source=qq&utm_medium=social)



**问题二：**

基本数据类型取值范围计算。

为什么byte类型的取值范围为-128 ~ 127 ?

```
byte在计算机中占1个字节(8位)，而且byte是有符号的，用二进制表示时，最高位为符号位，0表示正数，1表示负数
正数在计算机中，是以原码形式存在的，比如127用二进制表示为 0111 1111
负数在计算机中，是以补码形式存在的，即负数绝对值的原码按位取反后加1
       比如-127取绝对值为127，127用二进制表示为 0111 1111，按位取反后为 1000 0000，在加上1为 1000 0001
             -128取绝对值为128，128用二进制表示为 1000 0000，按位取反后为 0111 1111，再加上1为 1000 0000
             -129取绝对值为129，129用二进制表示未 1000 00001，长度超过8位了，所以byte类型最小值为-128
```

**问题三：**

switch能否用String做参数？

jdk1.7之前只能用基本数据类型里面的byte、short、int、char，以及枚举

jdk1.7之后可以用String类型

