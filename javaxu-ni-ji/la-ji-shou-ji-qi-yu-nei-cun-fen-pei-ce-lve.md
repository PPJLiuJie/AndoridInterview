## 如何判断对象已死

在堆里面存放着Java世界中几乎所有的对象实例，垃圾收集器在对堆进行回收前，第一件事情就是要确定这些对象之中哪些还“存活”着，哪些已经“死去”（即不可能再被任何途径使用的对象）。

### 1 引用计数法

给对象中添加一个引用计数器，每当有一个地方引用它时，计数器值就加1；当引用失效时，计数器值就减1；任何时刻计数器为0的对象就是不可能再被使用的。

客观地说，引用计数算法（Reference Counting）的实现简单，判定效率也很高，在大部分情况下它都是一个不错的算法。但是，至少主流的Java虚拟机里面没有选用引用计数算法来管理内存，其中最主要的原因是它很难解决对象之间相互循环引用的问题。举个例子：

```java
/**
 * testGC()方法执行后，objA和objB会不会被GC呢？
 * @author zzm
 */
public class ReferenceCountingGC {
    public Object instance = null;
    private static final int _1MB = 1024 * 1024;
    /**
     * 这个成员属性的唯一意义就是占点内存，以便能在GC日志中看清楚是否被回收过
     */
    private byte[] bigSize = new byte[2 * _1MB];

    /**
     * 对象objA和objB都有字段instance，赋值令objA.instance=objB及objB.instance=objA，
     * 除此之外，这两个对象再无任何引用，实际上这两个对象已经不可能再被访问，
     * 但是它们因为互相引用着对方，导致它们的引用计数都不为0，于是引用计数算法无法通知GC收集器回收它们。
     */

    public static void testGC() {
        ReferenceCountingGC objA = new ReferenceCountingGC();
        ReferenceCountingGC objB = new ReferenceCountingGC();
        objA.instance = objB;
        objB.instance = objA;
        objA = null;
        objB = null;
        // 假设在这行发生GC,objA和objB是否能被回收？
        System.gc();
    }
}
```

```
运行结果：
[F u l l G C（S y s t e m）[T e n u r e d：0 K-＞2 1 0 K（1 0 2 4 0 K），0.0 1 4 9 1 4 2 s e c s]4603K-＞210K（19456K），[Perm：2999K-＞
2999K（21248K）]，0.0150007 secs][Times：user=0.01 sys=0.00，real=0.02 secs]
Heap
def new generation total 9216K,used 82K[0x00000000055e0000，0x0000000005fe0000，0x0000000005fe0000）
Eden space 8192K，1%used[0x00000000055e0000，0x00000000055f4850，0x0000000005de0000）
from space 1024K，0%used[0x0000000005de0000，0x0000000005de0000，0x0000000005ee0000）
to space 1024K，0%used[0x0000000005ee0000，0x0000000005ee0000，0x0000000005fe0000）
tenured generation total 10240K,used 210K[0x0000000005fe0000，0x00000000069e0000，0x00000000069e0000）
the space 10240K，2%used[0x0000000005fe0000，0x0000000006014a18，0x0000000006014c00，0x00000000069e0000）
compacting perm gen total 21248K,used 3016K[0x00000000069e0000，0x0000000007ea0000，0x000000000bde0000）
the space 21248K，14%used[0x00000000069e0000，0x0000000006cd2398，0x0000000006cd2400，0x0000000007ea0000）
No shared spaces configured.
```

从运行结果中可以清楚看到，GC日志中包含“4603K-＞210K”，意味着虚拟机并没有因为这两个对象互相引用就不回收它们，这也从侧面说明虚拟机并不是通过引用计数算法来判断对象是否存活的。

### 2 可达性分析算法

在主流的商用程序语言（Java、 C\#等）的主流实现中，都是称通过可达性分析（Reachability Analysis）来判定对象是否存活的。 这个算法的基本思路就是通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链（Reference Chain），当一个对象到GC Roots没有任何引用链相连（用图论的话来说，就是从GC Roots到这个对象不可达）时，则证明此对象是不可用的。 如图所示，对象object 5、 object 6、 object 7虽然互相有关联，但是它们到GC Roots是不可达的，所以它们将会被判定为是可回收的对象。

![](/assets/QQ图片20180321110914.png)

在Java语言中，可作为GC Roots的对象包括下面几种：

1. 虚拟机栈（栈帧中的本地变量表）中引用的对象。
2. 方法区中类静态属性引用的对象。
3. 方法区中常量引用的对象。
4. 本地方法栈中JNI（即一般说的Native方法）引用的对象。

### 3 生成或死亡

即使在可达性分析算法中不可达的对象，也并非是“非死不可”的，这时候它们暂时处于“缓刑”阶段，要真正宣告一个对象死亡，至少要经历两次标记过程：

1. 如果对象在进行可达性分析后发现没有与GC Roots相连接的引用链，那它将会被第一次标记并且进行一次筛选，筛选的条件是此对象是否有必要执行finalize（）方法。 当对象没有覆盖finalize（）方法，或者finalize（）方法已经被虚拟机调用过，虚拟机将这两种情况都视为“没有必要执行”。
2. 如果这个对象被判定为有必要执行finalize（）方法，那么这个对象将会放置在一个叫做F-Queue的队列之中，并在稍后由一个由虚拟机自动建立的、 低优先级的Finalizer线程去执行它。 这里所谓的“执行”是指虚拟机会触发这个方法，但并不承诺会等待它运行结束，这样做的原因是，如果一个对象在finalize（）方法中执行缓慢，或者发生了死循环（更极端的情况），将很可能会导致F-Queue队列中其他对象永久处于等待，甚至导致整个内存回收系统崩溃。

finalize（）方法是对象逃脱死亡命运的最后一次机会，稍后GC将对F-Queue中的对象进行第二次小规模的标记，如果对象要在finalize（）中成功拯救自己——只要重新与引用链上的任何一个对象建立关联即可，譬如把自己（this关键字）赋值给某个类变量或者对象的成员变量，那在第二次标记时它将被移除出“即将回收”的集合；如果对象这时候还没有逃脱，那基本上它就真的被回收了。  

从代码中我们可以看到一个对象的finalize（）被执行，但是它仍然可以存活：

```java
/**
 * 此代码演示了两点： 
 * 1.对象可以在被GC时自我拯救。
 * 2.这种自救的机会只有一次，因为一个对象的finalize()方法最多只会被系统自动调用一次
 * @author zzm
 */
public class FinalizeEscapeGC {
	public static FinalizeEscapeGC SAVE_HOOK = null;

	public void isAlive() {
		System.out.println("yes,i am still alive：)");
	}

	@Override
	protected void finalize() throws Throwable {
		super.finalize();
		System.out.println("finalize mehtod executed！");
		FinalizeEscapeGC.SAVE_HOOK = this;
	}

	public static void main(String[] args) throws Throwable {
		SAVE_HOOK = new FinalizeEscapeGC();
		
		// 对象第一次成功拯救自己
		SAVE_HOOK = null;
		System.gc();
		// 因为finalize方法优先级很低，所以暂停0.5秒以等待它
		Thread.sleep(500);
		if (SAVE_HOOK != null) {
			SAVE_HOOK.isAlive();
		} else {
			System.out.println("no,i am dead：(");
		} 
		
		// 下面这段代码与上面的完全相同，但是这次自救却失败了
		SAVE_HOOK = null;
		System.gc();
		// 因为finalize方法优先级很低，所以暂停0.5秒以等待它
		Thread.sleep(500);
		if (SAVE_HOOK != null) {
			SAVE_HOOK.isAlive();
		} else {
			System.out.println("no,i am dead：(");
		}
	}
}
```

```
运行结果：
finalize mehtod executed！
yes,i am still alive：）
no,i am dead：（

从运行结果可以看出，SAVE_HOOK对象的finalize（）方法确实被GC收集器触发过，并且在被收集前成功逃脱了。
```

另外一个值得注意的地方是，代码中有两段完全一样的代码片段，执行结果却是一次逃脱成功，一次失败，这是因为任何一个对象的finalize（）方法都只会被系统自动调用一次，如果对象面临下一次回收，它的finalize（）方法不会被再次执行，因此第二段代码的自救行动失败了。

**需要特别说明的是，上面关于对象死亡时finalize（）方法的描述可能带有悲情的艺术色彩，笔者并不鼓励大家使用这种方法来拯救对象。 相反，笔者建议大家尽量避免使用它，因为它不是C/C++中的析构函数，而是Java刚诞生时为了使C/C++程序员更容易接受它所做出的一个妥协。 它的运行代价高昂，不确定性大，无法保证各个对象的调用顺序。 有些教材中描述它适合做“关闭外部资源”之类的工作，这完全是对这个方法用途的一种自我安慰。finalize（）能做的所有工作，使用try-finally或者其他方式都可以做得更好、 更及时，所以笔者建议大家完全可以忘掉Java语言中有这个方法的存在。**

