# 1. **概述：**

对于Java程序员来说，在虚拟机自动内存管理机制的帮助下，不再需要为每一个new操作去写配对的delete/free代码，不容易出现内存泄漏和内存溢出问题，由虚拟机管理内存这一切看起来都很美好。不过，也正是因为Java程序员把内存控制的权利交给了Java虚拟机，一旦出现内存泄漏和溢出方面的问题，如果不了解虚拟机是怎样使用内存的，那么排查错误将会成为一项异常艰难的工作。

# 2. **运行时数据区：**

Java虚拟机在执行Java程序的过程中会把它所管理的内存划分为若干个不同的数据区域。这些区域都有各自的用途，以及创建和销毁的时间，有的区域随着虚拟机的进程启动而存在，有些区域则依赖用户线程的启动和结束而建立和销毁。根据《Java虚拟机规范\(JavaSE 7版\)》的规定，Java虚拟机管理的内存将会包括以下几个运行时数据区。如下图所示：

![](/assets/00.png)

### 2.1 程序计算器

程序计算器\(Program Counter Register\)是一块较小的内存空间，它可以看做是当前线程所执行的字节码的行号指示器。


