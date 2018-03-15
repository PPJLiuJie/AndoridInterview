每个覆盖了equals方法的类中，也必须覆盖hashCode方法。如果不这样做的话，就会违反Object.hashCode的通用约定，从而导致该类无法结合所有基于散列的集合一起运作，这样的集合包括HashMap、HashSet和HashTable。摘自：《Effective Java 中文版 第2版》第9条

详细解释请参见：[https://github.com/Mr-YangCheng/ForAndroidInterview/blob/master/java/%5BJava%5D%20Hashcode%E7%9A%84%E4%BD%9C%E7%94%A8.md](https://github.com/Mr-YangCheng/ForAndroidInterview/blob/master/java/%5BJava%5D%20Hashcode%E7%9A%84%E4%BD%9C%E7%94%A8.md)

