
# GC

GC(Garbage Collection)这个概念并不是Java语言锁特有的技术。事实上GC的历史比Java久远，Lisp就才是第一门真正实用内存动态分配和垃圾收集技术的语言。在Java语言中，程序计数器，虚拟机栈，本地方法栈三个区域会随着线程的生命周期结束而得到清理，但是Java堆是动态分配的，垃圾处理器主要关注这部分内存。抽象来说GC要完成的有三件事情：

* 哪些内存需要回收
* 什么时候回收
* 如何回收

下面就按照上面三点来仔细说一下GC。

## 哪些内存需要回收

当一个对象再也没有引用可以引用到，那么我们就说它的生命周期可以结束了，那么如何去判断一个对象是否是已经无用了呢。

### 引用计数法

给引用加一个引用计数器，当计数器的数值为0就表示没有其它引用可以引用到这个对象，那么当然这个对象就可以安全的被垃圾回收器所回收。引用计数器实现简单，在大部分情况下它都是一个不错的算法，COM，FlashPlayer，Python等都使用了引用计数法金星内存管理。但是它有一个问题，就是很难解决对象之间的循环引用问题。这就类似于图论中的非连通有向图中的连通分量。

### 可达性分析算法

类似图论中的可达性，以引用为边，以对象为节点。算法的基本思想就是从一系列称为GC Roots的对象作为起始点，从这些节点向下搜索，如果一个节点不可达，则证明这个对象是不可用的，可以被安全的回收掉。在Java语言中，可以作为GC Roots的对象包括以下几种：
* 虚拟机栈（栈帧中的本地变量表）中引用的对象
* 方法区中静态类属性引用的对象
* 方法区中常量引用的对象
* 本地方法栈中JNI引用的对象

### 四种类型的引用

再JDK1.2之后，Java对引用的概念进行了扩充，将引用分为强引用，软引用，弱引用，虚引用，这四种引用的强度以此减弱。
* 强引用就是类似"Object obj = new Object()"这类的引用。只要强引用存在，垃圾收集器就不会回收调被引用的对象。
* 软引用是用来描述一些还有用但并非必须的对象。对于软引用关联的对象，再系统将要发生内存溢出之前会把这些对象列进回收范围之中进行二次回收，如果这次还没有足够的内存彩绘抛出内存溢出异常。详情见SoftReference。
* 弱引用是用来描述非必须对象，被弱引用关联的对象只能生存到下一次垃圾收集发生之前。当垃圾收集器工作时，无论内存是否足够都会回收被弱引用关联的对象。详情见WeakReference。
* 虚引用不影响对象的生存周期没有影响，为一个对象设置虚引用关联的唯一目的就是能在对象被收集的时候收到一个系统通知，详情见PhantomReference。

### finalize()方法
在可达性分析中不可达的对象也并不一定就会被回收。要真正的回收对象，至少要经历两次标记过程：如果一个对象被分析为不可达，那么它会被第一次标记，并且进行一次筛选，筛选条件是此对象是否有必要执行finalize()方法。当一个对象没有覆盖finalize()方法，或者finalize()方法已经被虚拟机调用过，那么虚拟机将这种情况视为没有必要执行。
如果这个对象有需要执行的finalize()方法，那么这个对象会被放置在一个叫F-Queue的队列中，并在稍后由一个虚拟机建立的，低优先级的Finalizer线程去执行它。但是要注意的是这里的执行只是表示虚拟机会去调用它，但是并不保证等待它执行完成。稍后虚拟机会对F-Queue中的对象进行第二次标记，如果这次标记的时候对象还是不可达，那么它将会被回收。

### 永久代的回收

### HotSpot虚拟机实现

#### 枚举根节点

#### OopMap

#### 安全点

#### 安全区
降至
#### 垃圾收集器

##### Serial收集器

##### ParNew收集器

##### Parallel收集器

##### ParallelOld收集器

##### GMS收集器

##### G1收集器





永久代回收，
  字面常量
  废弃的类，三个条件
  
hotspot实现
  枚举根节点 
    OopMap
  安全点
    指令流长时间执行，如方法调用，循环，异常跳转
    抢先式中断，主动式中断
  安全区

Remenbered Set

内存分配策略

