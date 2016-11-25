title: java面试
date: 2016-03-11 14:50:54
categories: [java]
tags:
---
### 并行计算与分布式计算
1.并行计算借助并行算法和并行编程语言能够实现进程及并行和线程级并行；而分布式计算只是将任务分成小块到各个计算机分别计算各自执行
2.粒度方面，并行计算中，处理器间的交互一般很频繁，往往具有细粒度和的开销的特征并且被认为是可靠的；分布式计算中，处理器间的交互不频繁，粗粒度，并被认为是不可靠的，并行计算注重短的执行时间，分布式计算则注重长的正常运行时间。
java中断机制是一种协作机制，也就是说通常并不能直接终止另一个线程，而需要被中断的线程自己处理中断

通常情况下，根线程组是System线程组，system线程组下是main线程组，默认情况下第一级应用自己的线程组是通过main线程组创建出来的。也就是说system线程组是最顶级的父线程组
Thread.currentThread().getThreadGroup()  // 获得当前线程的线程组
### 线程组和线程池的区别
线程组和线程池作用不同，前者是为了方便线程的管理，后者是为了管理线程的生命周期，复用线程，减少创建销毁线程的开销。
注意： ThreadLocalMap 对象是以this指向的ThreadLocal对象为键进行查找的，和set方法是相对应的。
每一个线程都有一个独立的ThreadLocalMap 副本，它所存储的值，只能被当前线程读取和修改。

### java内存模型
每个线程都有自己的工作内存，工作内存存储了主存的某些变量的副本，当然线程的工作内存是有限制的，当线程操作某个对象时，执行顺序如下：
1.从主存复制变量到当前工作内存 (read and load)
2.执行代码，改变共享变量   (use and assign)
3.用工作内存数据刷新主存相关的内容   (store and write)
线程在引用变量时不能直接从内存中引用，如果线程工作内存中没有该变量，会从主存拷贝一个副本到工作内存中，read and load ，完成后线程会引用该副本，当同一线程再度引用该字段时，可能重新从主存获取变量副本 read load use ,也有可能直接引用原来的的副本，也就是说read load use顺序由JVM决定。产生时序性问题。

加锁和释放锁都需要此对象的资源，那肯定对象越小越好，所以方法块加锁使用如下：
```
private byte[] lock = new byte[1];
public void methodB(){
	synchronized(lock){

	}
}
```
ReentrantLock 是工作中使用频率最高的方法块加锁
### Lock 与 synchronized 区别
1. Lock使用起来更灵活，但是必须有释放锁的动作配合
2. Lock必须手动释放和开启锁，而synchronized不需要手动释放和开启锁
3. Lock只适用于代码块锁，而synchronized对象之间是互斥关系

### 单例模式
```
class Singleton{
	private static Singleton instance;
	private static byte[] lock = new byte[1];
	private Singleton{ }
	public static Singleton getInstance(){
		if(instance == null){
			synchronized(lock){
				if(instance == null) {
					instance = new Singleton();
				}
			}
		}
		return instance;
	}
}
```
### CopyOnWrite机制
CopyOnWrite 写时复制的容器，当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器copy，复制出一个新的容器，然后新的容器里
、添加元素，添加完元素之后，再将当前容器的引用指向新的容器。

线程池一定要在合理的单例模式下才有效
### 线程池的好处
1.降低资源消耗，重用已经创建好的线程，降低创建和销毁的开销
2.提高响应速度，不用等待创建线程就能立即执行
3.提高线程的可管理性，统一分配、调优和监控
4.防止服务器过载，防止内存溢出或者CPU耗尽

### 对象引用的类型
Reference(or named Strong Reference)（ 强引用）：普通类型的引用。
SoftReference（ 软引用）：被这种引用指向的对象，如果此对象没要再被其他Strong Reference引用的话，可能在任何时候被GC。虽然是可能在任何时候被GC，但是通常是在可用内存数比较低的时候，并且在程序抛出OutOfMemoryError之前才发生对此对象的GC。SoftReference通常被用作实现Cache的对象引用，如果这个对象被GC了，那么他可以在任何时候再重新被创建。另外，根据JDK文档中介绍，实际JVM的实现是鼓励不回收最近创建和最近使用的对象。SoftReference 类的一个典型用途就是用于内存敏感的高速缓存。
WeakReference（弱引用）：如果一个被WeakReference引用的对象，当没要任何SoftReference和StrongReference引用时，立即会被GC。和SoftReference的区别是：WeakReference对象是被eagerly collected，即一旦没要任何SoftReference和StrongReference引用，立即被清楚；而只被SoftReference引用的对象，不回立即被清楚，只有当内存不够，即将发生OutOfMemoryError时才被清除，而且是先清除不常用的。SoftReference适合实现Cache用。WeakReference 类的一个典型用途就是规范化映射（ canonicalized mapping ）
PhantomReference（虚引用）：当没有StrongReference，SoftReference和WeakReference引用时，随时可被GC。通常和ReferenceQueue联合使用，管理和清除与被引用对象（没有finalize方法）相关的本地资源。

### 简单工厂 工厂方法 抽象工厂
简单工厂: 抽象了同一类型(比如不同品牌的笔记本电脑)产品的生产过程,通过工厂类来实现对不同产品生产过程的选择
工厂方法: 不仅抽象了产品类的生产过程,还抽象了工厂类本身. 将特定的产品的生产过程和特定的工厂相对应. 将原先工厂类中的选择提升到工程类以上的层级---即客户层级. 通过选择特定的工厂,来选择生产特定的产品.
抽象工厂: 在工厂方法的基础上,将特定的产品生产过程和特定的工厂类之间的对应关系解除,使得工厂类和产品类之间能够产生更多灵活的组合方式.












