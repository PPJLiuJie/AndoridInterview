foreach语句是Java5的新特性，在遍历数组、集合等方面，foreach为开发人员提供了极大的方便。

foreach语句是for语句的特殊简化版本，但是foreach语句并不能完全取代for语句，然而，任何的foreach语句都可以改写为for语句版本。

其实foreach的内部原理其实也是Iterator,但它不能像Iterator一样可以人为的控制，而且也不能调用iterator.remove\(\)；更不能使用下标来访问每个元素，所以不能用于增加，删除等复杂的操作。

以上内容摘自：[http://blog.csdn.net/heyuchang666/article/details/49683615](http://blog.csdn.net/heyuchang666/article/details/49683615)

foreach的实现原理，参见：[http://www.hollischuang.com/archives/1776](http://www.hollischuang.com/archives/1776)

foreach和for效率问题，参见：[http://blog.csdn.net/qq\_27093465/article/details/52044687](http://blog.csdn.net/qq_27093465/article/details/52044687)

