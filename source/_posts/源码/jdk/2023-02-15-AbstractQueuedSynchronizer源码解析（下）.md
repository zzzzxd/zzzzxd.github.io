---
title: AbstractQueuedSynchronizer源码解析（下）
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2023-02-15 11:28:29
subtitle:
tags:
- java
- jdk源码
categories:
- 源码
  - jdk
---

## 释放锁

释放锁的触发时机就是我们常用的 Lock.unLock () 方法，目的就是让线程释放对资源的访问权 （流程见整体架构图紫色路线）。

释放锁也是分为两类，一类是排它锁的释放，一类是共享锁的释放，我们分别来看下。

### 释放排它锁 release

排它锁的释放就比较简单了，从队头开始，找它的下一个节点，如果下一个节点是空的，就会从 尾开始，一直找到状态不是取消的节点，然后释放该节点，源码如下：

```java
 //unlock的基础方法
    public final boolean release(int arg) {
        // tryRelease 交给实现类去实现，一般就是用当前同步器状态减去 arg，如果返回 true 说明释放锁了
        if (tryRelease(arg)) {
            Node h = head;
            // 头节点不为空，并且不是同步节点，就去释放
            if (h != null && h.waitStatus != 0)
                //从头开始唤醒等待锁的节点
                unparkSuccessor(h);
            return true;
        }
        return false;
    }
```

```java
    // 很有意思的方法，当线程释放锁成功后，从 node 开始唤醒同步队列中的节点
    // 通过唤醒机制,保证线程不会一直在同步队列中阻塞等待
    private void unparkSuccessor(Node node) {
        int ws = node.waitStatus;
        // node 节点是当前释放锁的节点
        // 把节点的状态置为初始化
        if (ws < 0)
            compareAndSetWaitStatus(node, ws, 0);

        // 拿出 node 节点的后面一个节点
        Node s = node.next;
        // s 为空，代表队列中没有其他等待的节点了
        // s.waitStatus 大于0，代表 s 节点已经被取消了
        // 遇到以上这两种情况，就从队尾开始，向前遍历，找到第一个 waitStatus 不是取消的
        if (s == null || s.waitStatus > 0) {
            s = null;
            // 这里从尾迭代，而不是从头开始迭代是有原因的。
            // 主要是因为节点被阻塞的 acquireQueued 方法里面，节点在 acquireQueued 方法被阻塞，唤醒时也一定会在
            // acquireQueued 方法里面被唤醒，唤醒之后的条件是，判断当前节点的前置节点是否是头节点，这里是判断当前节点的
            // 前置节点，所以这里必须使用从尾到头的迭代顺序才行
            for (Node t = tail; t != null && t != node; t = t.prev)
                // t.waitStatus <= 0 说明 t 没有被取消，肯定还在等待被唤醒
                if (t.waitStatus <= 0)
                    s = t;
        }
        // 唤醒以上代码找到的线程
        if (s != null)
            LockSupport.unpark(s.thread);
    }
```

这个函数并不复杂。一句话概括：用unpark()唤醒等待队列中最前边的那个未放弃线程，这里我们也用s来表示吧。此时，再和acquireQueued()联系起来，s被唤醒后，进入if (p == head && tryAcquire(arg))的判断（即使p!=head也没关系，它会再进入shouldParkAfterFailedAcquire()寻找一个安全点。这里既然s已经是等待队列中最前边的那个未放弃线程了，那么通过shouldParkAfterFailedAcquire()的调整，s也必然会跑到head的next结点，下一次自旋p==head就成立啦），然后s把自己设置成head标杆结点，表示自己已经获取到资源了，acquire()也返回了

### 释放共享锁 releaseShared

释放共享锁的方法是 releaseShared，主要分成两步：

1. tryReleaseShared 尝试释放当前共享锁，失败返回 false，成功走 2；

2. 唤醒当前节点的后续阻塞节点，这个方法我们之前看过了，线程在获得共享锁的时候，就 会去唤醒其后面的节点，方法名称为：doReleaseShared。

我们一起来看下 releaseShared 的源码：

```java
  // 共享模式下的释放
    public final boolean releaseShared(int arg) {
        if (tryReleaseShared(arg)) {
            // 这个方法就是线程在获得锁时，唤醒后续节点时调用的方法
            doReleaseShared();
            return true;
        }
        return false;
    }
```

## 条件队列

最后我们再来细说一下条件队列。在看条件队列的方法之前，我们先得弄明白为什么有了同步队列，还需要条件队列？

简单讲，一些线程在获得锁之后 发现某些条件不满足，进行自我阻塞，此时用的队列就是条件队列。比如，获得锁的多个线程在碰到队列满或者空的时候，可以使用 Condition 来 管理这些线程，让这些线程阻塞等待，然后在合适的时机后，被正常唤醒。

### Condition

刚才我们看条件队列 ConditionObject 时，发现其是实现 Condition 接口的，现在我们一起来 看下 Condition 接口，其类注释上是这么写的：

1. 当 lock 代替 synchronized 来加锁时，Condition 就可以用来代替 Object 中相应的监控 方法了，比如 Object#wait ()、Object#notify、Object#notifyAll 这些方法；
2. 提供了一种线程协作方式：一个线程被暂停执行，直到被其它线程唤醒；
3. Condition 实例是绑定在锁上的，通过 Lock#newCondition 方法可以产生该实例；

类注释上甚至还给我们举了一个例子：

假设我们有一个有界边界的队列，支持 put 和 take 方法，需要满足： 1：如果试图往空队列上执行 take，线程将会阻塞，直到队列中有可用的元素为止； 2：如果试图往满的队列上执行 put，线程将会阻塞，直到队列中有空闲的位置为止。

1、2 中线程阻塞都会到条件队列中去阻塞。

take 和 put 两种操作如果依靠一个条件队列，那么每次只能执行一种操作，所以我们可以新建 两个条件队列，这样就可以分别执行操作了， 感兴趣的可以看看 ConditionDemo 这个测试类d除了类注释，Condition 还定义出一些方法，这些方法奠定了条件队列的基础，方法主要有：

```java
void await() throws InterruptedException;
```

这个方法的主要作用是：使当前线程一直等待，直到被 signalled 或被打断。

当以下四种情况发生时，条件队列中的线程将被唤醒

1. 有线程使用了 signal 方法，正好唤醒了条件队列中的当前线程；
2. 有线程使用了 signalAll 方法；
3. 其它线程打断了当前线程，并且当前线程支持被打断；
4. 被虚假唤醒 (即使没有满足以上 3 个条件，wait 也是可能被偶尔唤醒，虚假唤醒定义可以 参考： https://en.wikipedia.org/wiki/Spurious_wakeup)。

被唤醒时，有一点需要注意的是：线程从条件队列中苏醒时，必须重新获得锁，才能真正被唤 醒，这个我们在说源码的时候，也会强调这个。

await 方法还有带等待超时时间的，如下：

```java
// 返回的 long 值表示剩余的给定等待时间，如果返回的时间小于等于 0 ，说明等待时间过了 // 选择纳秒是为了避免计算剩余等待时间时的截断误差 
long awaitNanos(long nanosTimeout) throws InterruptedException;

// 虽然入参可以是任意单位的时间，但底层仍然转化成纳秒 
boolean await(long time, TimeUnit unit) throws InterruptedException;
```

除了等待方法，还是唤醒线程的两个方法，如下：

```java
// 唤醒条件队列中的一个线程，在被唤醒前必须先获得锁 
void signal();

// 唤醒条件队列中的所有线程 
void signalAll();
```

接下来我们来看一下条件队列一些比较重要的方法， 以下方法都在 ConditionObject 内部类 中。

### 入队列等待 await

获得锁的线程，如果在碰到队列满或空的时候，就会阻塞住，这个阻塞就是用条件队列实现的， 这个动作我们叫做入条件队列，方法名称为 await，流程见整体架构图中深绿色箭头流向，我们 一起来看下 await 的源码：

```java
        public final void await() throws InterruptedException {
            if (Thread.interrupted())
                throw new InterruptedException();
            // 加入到条件队列的队尾
            Node node = addConditionWaiter();
            // 加入条件队列后，会释放lock申请的资源，唤醒同步队列队列头的节点
            // 自己马上就要阻塞了，必须马上释放之前lock的资源，不然自己不被唤醒，别的线程永远得不到该共享资源了
            int savedState = fullyRelease(node);
            int interruptMode = 0;
            // 确认node不在同步队列上，再阻塞，如果 node 在同步队列上，是不能够上锁的
            // 这里的情况比较特殊，目前想到的只有一种可能：
            // node刚被加入到条件队列中，立马就被其他线程signal转移到同步队列中去了
            while (!isOnSyncQueue(node)) {
                // this = AbstractQueuedSynchronizer$ConditionObject
                // 阻塞在条件队列上
                LockSupport.park(this);
                if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
                    break;
            }
            // 其他线程通过 signal 已经把 node 从条件队列中转移到同步队列中的数据结构中去了
            // 所以这里节点苏醒了，直接尝试 acquireQueued
            if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
                interruptMode = REINTERRUPT;
            if (node.nextWaiter != null) // clean up if cancelled
                // 如果状态不是CONDITION，就会自动删除
                unlinkCancelledWaiters();
            if (interruptMode != 0)
                reportInterruptAfterWait(interruptMode);
        }
```

await 方法有几点需要特别注意：

1. ，节点在准备进入条件队列之前，一定会先释放当前持有的锁，不 然自己进去条件队列了，其余的线程都无法获得锁了；
2. Node 在条件队列中的命名，源码喜欢用 Waiter 来命名，所以我们在条件队列中看到 Waiter，其实就是 Node。

await 方法中有两个重要方法：addConditionWaiter 和 unlinkCancelledWaiters， 我们一一 看下。

#### addConditionWaiter

addConditionWaiter 方法主要是把节点放到条件队列中，方法源码如下：

```java
        // 增加新的 waiter 到队列中，返回新添加的 waiter
        // 如果尾节点状态不是 CONDITION 状态，删除条件队列中所有状态不是 CONDITION 的节点
        // 如果队列为空，新增节点作为队列头节点，否则追加到尾节点上
        private Node addConditionWaiter() {
            Node t = lastWaiter;
            // If lastWaiter is cancelled, clean out.
            // 如果尾部的 waiter 不是 CONDITION 状态了，删除
            if (t != null && t.waitStatus != Node.CONDITION) {
                unlinkCancelledWaiters();
                t = lastWaiter;
            }
            // 新建条件队列 node
            Node node = new Node(Thread.currentThread(), Node.CONDITION);
            // 队列是空的，直接放到队列头
            if (t == null)
                firstWaiter = node;
            // 队列不为空，直接到队列尾部
            else
                t.nextWaiter = node;
            lastWaiter = node;
            return node;
        }
```

整 体 过 程 比 较 简 单 ， 就 是 追 加 到 队 列 的 尾 部 ， 其 中 有 个 重 要 方 法 叫 做 unlinkCancelledWaiters，这个方法会删除掉条件队列中状态不是 CONDITION 的所有节点， 我们来看下 unlinkCancelledWaiters 方法的源码，如下：

#### unlinkCancelledWaiters

```java
        // 会检查尾部的 waiter 是不是已经不是CONDITION状态了
        // 如果不是，删除所有的waiter
        private void unlinkCancelledWaiters() {
            Node t = firstWaiter;
            // 上一个状态是,这个字段作用非常大，可以把状态都是 CONDITION 的 node 串联起来，即使 node 之间有其他节点都可以
            Node trail = null;
            while (t != null) {
                Node next = t.nextWaiter;
                // 当前node的状态不是CONDITION，删除自己
                if (t.waitStatus != Node.CONDITION) {
                    //删除当前node
                    t.nextWaiter = null;
                    // 如果 trail 是空的，咱们循环又是从头开始的，说明从头到当前节点的状态都不是 CONDITION
                    // 都已经被删除了，所以移动队列头结点到当前节点的下一个节点
                    if (trail == null)
                        firstWaiter = next;
                    // 如果找到上次状态是CONDITION的节点的话，先把当前节点删掉，然后把自己挂到上一个状态是 CONDITION 的节点上
                    else
                        trail.nextWaiter = next;
                    // 遍历结束，最后一次找到的CONDITION节点就是尾节点
                    if (next == null)
                        lastWaiter = trail;
                }
                // 状态是 CONDITION 的 Node
                else
                    trail = t;
                // 继续循环，循环顺序从头到尾
                t = next;
            }
        }

```

为了方便大家理解这个方法，画了一个释义图，如下：

![image-20230220102412997](image-20230220102412997.png)

### 单个唤醒 signal

还是以阻塞队列为例，如果B线程生产完元素后 进行自我阻塞，突然队列中的元素被线程 A 消费了，线程 A 就会调用 signal 方法，唤醒之前阻塞的线 程，会从条件队列的头节点开始唤醒（流程见整体架构图中蓝色部分），源码如下：

```java
  // 唤醒阻塞在条件队列中的节点
  public final void signal() {
      if (!isHeldExclusively())
          throw new IllegalMonitorStateException();
      // 从头节点开始唤醒
      Node first = firstWaiter;
      if (first != null)
          // doSignal 方法会把条件队列中的节点转移到同步队列中去
          doSignal(first);
  }
  // 把等待队列头节点转移到同步队列去
  private void doSignal(Node first) {
    do {
      // nextWaiter为空，说明到队尾了
      if ( (firstWaiter = first.nextWaiter) == null)
        lastWaiter = null;
      // 从队列头部开始唤醒，所以直接把头结点.next 置为 null，这种操作其实就是把 node 从条件队列中移除了
      // 这里有个重要的点是，每次唤醒都是从队列头部开始唤醒，所以把 next 置为 null 没有关系，如果唤醒是从任意节点
      // 开始唤醒的话，就会有问题，容易造成链表的割裂
      first.nextWaiter = null;
      // 通过while保证transferForSignal能成功
      // (first = firstWaiter) != null  = true 的话，表示还可以继续循环， = false 说明队列中的元素已经循环完了
    } while (!transferForSignal(first) &&
             (first = firstWaiter) != null);
  }
```

我们来看下最关键的方法：transferForSignal。

```java
// 返回 true 表示转移成功， false 失败
// 大概思路：
// 1. node 追加到同步队列的队尾
// 2. 将 node 的前一个节点状态置为 SIGNAL，成功直接返回，失败直接唤醒
// 可以看出来 node 的状态其实仍然是 CONDITION
final boolean transferForSignal(Node node) {
  /*
         * If cannot change waitStatus, the node has been cancelled.
         */
  // 将 node 的状态从 CONDITION 修改成初始化，失败返回 false
  if (!compareAndSetWaitStatus(node, Node.CONDITION, 0))
    return false;

  /*
         * Splice onto queue and try to set waitStatus of predecessor(前任) to
         * indicate(表明) that thread is (probably) waiting. If cancelled or
         * attempt to set waitStatus fails, wake up to resync (in which
         * case the waitStatus can be transiently and harmlessly wrong).
         */
  // 当前队列加入到同步队列，返回的 p 是 node 在同步队列中的前一个节点
  // 看命名是 p，实际是 pre 前一个单词的缩写
  Node p = enq(node);
  int ws = p.waitStatus;
  // 状态修改成 SIGNAL，如果成功直接返回
  // 把当前节点的前一个节点修改成 SIGNAL 的原因，是因为 SIGNAL 本身就表示当前节点后面的节点都是需要被唤醒的
  if (ws > 0 || !compareAndSetWaitStatus(p, ws, Node.SIGNAL))
    // 如果 p 节点被取消，或者状态不能修改成SIGNAL，直接唤醒
    LockSupport.unpark(node.thread);
  return true;
}
```

### 全部唤醒 signalAll

```java
// 唤醒全部
public final void signalAll() {
  if (!isHeldExclusively())
    throw new IllegalMonitorStateException();
  // 拿到头节点
  Node first = firstWaiter;
  if (first != null)
    // 从头节点开始唤醒条件队列中所有的节点
    doSignalAll(first);
}
// 把等待队列所有节点依次转移到同步队列去
private void doSignalAll(Node first) {
  lastWaiter = firstWaiter = null;
  do {
    // 拿出条件队列队列头节点的下一个节点
    Node next = first.nextWaiter;
    // 把头节点从同步队列中删除
    first.nextWaiter = null;
    // 头节点转移到同步队列中去
    transferForSignal(first);
    // 开始循环头节点的下一个节点
    first = next;
  } while (first != null);
}
```

