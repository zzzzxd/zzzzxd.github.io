---
title: Synchronized关键字原理
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-09-25 09:20:59
subtitle:
tags:
  -	JVM
categories:
  -	jvm
---

## 前言

​	java聊到并发，synchronized关键字想必肯定是再熟悉不过了，它的使用非常简单，接下来我们就重点看一下使用时候的注意点和原理。

## 使用总结

1. 选用一个锁对象，可以是任意对象；
2. 锁对象锁的是同步代码块，并不是自己；
3. 不同类型的多个 Thread 如果有代码要同步执行，锁对象要使用所有线程共同持有的同一个对象；
4. 需要同步的代码放到大括号中。需要同步的意思就是需要保证原子性、可见性、有序性中的任何一种或多种。不要放不需要同步的代码进来，影响代码效率。

## Synchronized原理

### 字节码实现

​	Synchronized在JVM里的实现都是基于进入和退出Monitor对象来实现方法同步和代码块同步，虽然具体实现细节不一样，但是都可以通过成对的MonitorEnter和MonitorExit指令来实现。

​	<font color=red>monitorenter：每个对象都是一个监视器锁（monitor）</font>。

当monitor被占用时就会处于锁定状态，线程执行monitorenter指令时尝试获取monitor的所有权，过程如下：

1. 如果monitor的进入数为0，则该线程进入monitor，然后将进入数设置为1，该线程即为monitor的所有者;
2. 如果线程已经占有该monitor，只是重新进入，则进入monitor的进入数加1；
3.  如果其他线程已经占用了monitor，则该线程进入阻塞状态，直到monitor的进入数为0，再重新尝试获取monitor的所有权；

monitorexit：执行monitorexit的线程必须是objectref所对应的monitor的所有者。指令执行时，monitor的进入数减1，如果减1后进入数为0，那线程退出monitor，不再是这个monitor的所有者。其他被这个monitor阻塞的线程可以尝试去获取这个 monitor 的所有权。
monitorexit，指令出现了两次，第1次为同步正常退出释放锁；第2次为发生异步退出释放锁；

通过上面描述，我们应该能很清楚的看出Synchronized的实现原理，**Synchronized的语义底层是通过一个monitor的对象来完成，其实wait/notify等方法也依赖于monitor对象，这就是为什么只有在同步的块或者方法中才能调用wait/notify等方法，**否则会抛出java.lang.IllegalMonitorStateException的异常的原因。
方法的同步：（加了Synchronized的方法）

​	两个指令的执行是JVM通过调用操作系统的互斥原语mutex来实现，被阻塞的线程会被挂起、等待重新调度，会导致“用户态和内核态”两个态之间来回切换，对性能有较大影响。

### Mointor

​	可以把它理解为 一个同步工具，也可以描述为 一种同步机制，它通常被 描述为一个对象。与一切皆对象一样，所有的Java对象是天生的Monitor，每一个Java对象都有成为Monitor的潜质，因为在Java的设计中 ，每一个Java对象自打娘胎里出来就带了一把看不见的锁，它叫做内部锁或者Monitor锁。也就是通常说Synchronized的对象锁，MarkWord锁标识位为10，其中指针指向的是Monitor对象的起始地址。在Java虚拟机（HotSpot）中，Monitor是由ObjectMonitor实现的，其主要数据结构如下（位于HotSpot虚拟机源码ObjectMonitor.hpp文件，C++实现的）：

ObjectMonitor中有两个队列，_WaitSet 和 _EntryList，用来保存ObjectWaiter对象列表（ 每个等待锁的线程都会被封装成
ObjectWaiter对象 ），_owner指向持有ObjectMonitor对象的线程，当多个线程同时访问一段同步代码时：

	1.	首先会进入 _EntryList 集合，当线程获取到对象的monitor后，进入 _Owner区域并把monitor中的owner变量设置为当前线程，同时monitor中的计数器count加1；
 	2.	若线程调用 wait() 方法，将释放当前持有的monitor，owner变量恢复为null，count自减1，同时该线程进入 WaitSet集合中等待被唤醒；
 	3.	若当前线程执行完毕，也将释放monitor（锁）并复位count的值，以便其他线程进入获取monitor(锁)；
     当多个线程同时请求某个对象锁时，对象锁会设置⼏种状态⽤来区分请求的线程：
     Contention List：所有请求锁的线程将被⾸先放置到该竞争队列
     Entry List：Contention List中那些有资格成为候选⼈的线程被移到Entry List
     Wait Set：那些调⽤wait⽅法被阻塞的线程被放置到Wait Set
     OnDeck：任何时刻最多只能有⼀个线程正在竞争锁，该线程称为OnDeck
     Owner：获得锁的线程称为Owner
     !Owner：释放锁的线程
     当⼀个线程尝试获得锁时，如果该锁已经被占⽤，则会将该线程封装成⼀个 ObjectWaiter 对象插⼊到Contention List的队列的队⾸，然后调⽤ park 函数挂起当前线程。

当线程释放锁时，会从Contention List或EntryList中挑选⼀个线程唤醒，被选中的线程叫做 Heir presumptive 即假定继承⼈，假定继承⼈被唤醒后会尝试获得锁，但 synchronized 是⾮公平的，所以假定继承⼈不⼀定能获得锁。这是因为对于重量级锁，线程先⾃旋尝试获得锁，这样做的⽬的是为了减少执⾏操作系统同步操作带来的开销。如果⾃旋不成功再进⼊等待队列。这对那些已经在等待队列中的线程来说，稍微显得不公平，还有⼀个不公平的地⽅是⾃旋线程可能会抢占了Ready线程的锁。线程获得锁后调⽤ Object.wait ⽅法，则会将线程加⼊到WaitSet中，当被 Object.notify 唤醒后，会将线程从WaitSet移动到Contention List或EntryList中去。需要注意的是，当调⽤⼀个锁对象的 wait 或 notify ⽅法时，如当前锁的状态是偏向锁或轻量级锁则会先膨胀成重量级锁。

同时，Monitor对象存在于每个Java对象的对象头Mark Word中（存储的指针的指向），Synchronized锁便是通过这种方式
获取锁的，也是为什么Java中任意对象可以作为锁的原因，同时notify/notifyAll/wait等方法会使用到Monitor锁对象，所以必须在同步代码块中使用。监视器Monitor有两种同步方式：互斥与协作。多线程环境下线程之间如果需要共享数据，需要解决互斥访问数据的问题，监视器可以确保监视器上的数据在同一时刻只会有一个线程在访问。

## 使用注意

1. synchronized 使用的为非公平锁，如果你需要公平锁，那么不要使用 synchronized。可以使用 ReentrantLock，设置为公平锁。关于 ReentrantLock。
2. 锁对象不能为 null。如果锁对象为 null，何谈对象头，以及保存与其关联的 monitor 锁呢？所以代码中要确保synchronized使用的锁对象不为 null；
3. 只把需要同步的代码放入 synchronized 代码块。如果不思考，为了线程安全把方法中全部代码都放入同步代码块，那么将会丧失多线程的优势。再多的线程也只能串行执行，这完全违背了并发的初衷；
4. 只有使用同一个对象作为锁对象，才能同步。记住是同一个对象，而不是同一个类。有一种常犯的错误是，不同线程持有的是同一个类的不同实例。那么该对象实例用作锁对象的话，多个线程并不会同步。还一种错误是使用不同类的实例作为锁对象，但是期望不同位置的同步代码块能够同步执行。这是不可能达到你想要的效果的。

>参考:https://www.cnblogs.com/freelancy/p/15625602.html
>
>​		https://www.jianshu.com/p/b624ca34fee2

