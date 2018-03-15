**OOM\(OutOfMemoryError\):**

什么情况下会出现OOM？

1. 内存中加载的数据量过于庞大，如一次从数据库读取过多的数据
2. 集合类中有对对象的引用，使用完后未清空，集合对象会始终持有集合中元素的引用，导致JVM无法回收
3. 代码中存在死循环\(递归\)或循环产生过多的重复对象
4. 启动参数内存值设定的过小

摘自：[http://outofmemory.cn/c/java-outOfMemoryError](http://outofmemory.cn/c/java-outOfMemoryError)

另外，关于OOM，这篇博客分析的很全面：[https://www.jianshu.com/p/2fdee831ed03](https://www.jianshu.com/p/2fdee831ed03)



**SOF\(StackOverFlow\):**

什么情况下会出现SOF？

当应用程序递归太深而发生堆栈溢出时，抛出该错误。因为栈一般默认为1-2M，一旦出现死循环或者是大量的递归调用，在不断的压栈过程中，造成栈容量超过1M而导致溢出。

摘自：[https://yq.aliyun.com/articles/52841](https://yq.aliyun.com/articles/52841)

