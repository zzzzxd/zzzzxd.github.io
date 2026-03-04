---
title: AbstractQueuedSynchronizer源码解析（上）
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2023-02-15 11:28:22
subtitle:
tags:
- java
- jdk源码
categories:
- 源码
  - jdk
---

>AQS 同步器 是一种模版方法的设计，事先准备了一些锁相关的方法 ，juc包中的很多锁都是利用AQS实现，如下图。如果我们掌握了AQS原理 也能实现一个锁。AQS方法较多 ，对于源码学习而言，一定要先熟悉原理，从原理入手，串通整个流程，放眼观全局，一定不要掉入到某个细节之中。

![在这里插入图片描述](watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTI2NTcz,size_16,color_FFFFFF,t_70.png)

## 整体结构

![image-20230215124430127](image-20230215124430127.png)

这个图总结了 AQS 整体架构的组成，和部分场景的动态流向，图中两个点说明一下，方便大家 观看。

1. AQS 中队列只有两个：同步队列 + 条件队列，底层数据结构两者都是链表；
2. 图中有四种颜色的线代表四种不同的场景，1、2、3 序号代表看的顺序。
2. 对于条件队列，大家可以咱先不关注，条件队列的作用类似 对象中的wait()和notify()方法，这个我们会单独进行讲解，为了更好的理解AQS，我们先重点关注 同步队列。
4. 那么 我们对AQS的作用，进行一个概括就是：
   1. **通过一个共享变量，能够完成 线程的互斥**。
   2. **抢不到资源的线程要被阻塞，存放进同步队列，并且资源释放时能被唤醒**。


### 类注释信息

养成好习惯，我们还是看一下AQS的注释信息，可以更好的理解AQS的作用。 

1. 提供了一种框架，自定义了先进先出的同步队列，让获取不到锁的线程能进入同步队列中 排队；
2. 同步器有个状态字段，我们可以通过状态字段来判断能否得到锁，此时设计的关键在于依 赖安全的 atomic value 来表示状态（虽然注释是这个意思，但实际上是通过把状态声明 为 volatile，在锁里面修改状态值来保证线程安全的）；
3. 子类可以通过给状态 CAS 赋值来决定能否拿到锁，可以定义那些状态可以获得锁，哪些 状态表示获取不到锁（比如定义状态值是 0 可以获得锁，状态值是 1 就获取不到锁）；
4. 子类可以新建非 public 的内部类，用内部类来继承 AQS，从而实现锁的功能；
5. AQS 提供了排它模式和共享模式两种锁模式。排它模式下：只有一个线程可以获得锁，共 享模式可以让多个线程获得锁，子类 ReadWriteLock 实现了两种模式；
6. 内部类 ConditionObject 可以被用作 Condition，我们通过 new ConditionObject () 即 可得到条件队列；
7. AQS 实现了锁、排队、锁队列等框架，至于如何获得锁、释放锁的代码并没有实现，比如 tryAcquire、tryRelease、tryAcquireShared、tryReleaseShared、isHeldExclusively 这些方法，AQS 中默认抛 UnsupportedOperationException 异常，都是需要子类去实 现的；
8. AQS 继承 AbstractOwnableSynchronizer 是为了方便跟踪获得锁的线程，可以帮助监 控和诊断工具识别是哪些线程持有了锁；
9. AQS 同步队列和条件队列，获取不到锁的节点在入队时是先进先出，但被唤醒时，可能并 不会按照先进先出的顺序执行。

​	以上时AQS中比较重要的类信息。

### 定义 

AQS 类定义代码如下：

```java
public abstract class AbstractQueuedSynchronizer
    extends AbstractOwnableSynchronizer
    implements java.io.Serializable {
```

可以得出两点

1. AQS 是个抽象类，就是给各种锁子类继承用的，AQS 定义了很多如何获得锁，如何释放 锁的抽象方法，目的就是为了让子类去实现；

2. 继承了 AbstractOwnableSynchronizer，AbstractOwnableSynchronizer 的作用就是为 了知道当前是那个线程获得了锁，方便监控用的，代码如下：

   <img src="image-20230215134221441.png" alt="image-20230215134221441" style="zoom:40%;" />

## 类属性

AQS 的属性可简单分为四类：同步器简单属性、同步队列属性、条件队列属性、公用 Node。

### 简单属性

```java
// 同步器的状态，根据当前状态进行判断是否可以获得当前锁
// 如果当前state是0，那么可以获得锁
// 可重入锁，每次获得锁+1，每次释放锁-1
private volatile int state;
// 自旋超时阀值，单位纳秒
// 当设置等待时间时才会用到这个属性
static final long spinForTimeoutThreshold = 1000L;
```

最重要的就是 state 属性，是 int 属性的，所有继承 AQS 的锁都是通过这个字段来判断能不能 获得锁，能不能释放锁。state就是咱们之前提的共享资源。

### 同步队列属性

线程获得不到锁时，就会封装成Node进入到同步队列的队尾。释放锁时，就会从同步队列头开始释放一个排队的线程，让线程重新去竞争锁。所以同步队列的主要作用阻塞获取不到锁的线程，并在适当时机释放这些线程。同步队列底层数据结构是个双向链表，我们从源码中可以看到链表的头尾，如下：

```java
// 同步队列的头。
// 公平的锁先入先出。
private transient volatile Node head;

// 等待队列的尾
private transient volatile Node tail;
```

源码中的 Node 是同步队列中的元素，但 Node 被同步队列和条件队列公用，所以我们在说完 条件队列属性之后再说 Node。

### 条件队列属性

条件队列和同步队列的功能一样，管理获取不到锁的线程，底层数据 结构也是链表队列，但条件队列不直接和锁打交道，但常常和锁配合使用，是一定的场景下，对 锁功能的一种补充。此外，条件队列底层是单链表，后面会详细介绍。

```java
// 条件队列，从基础属性上可以看出是链表队列
public class ConditionObject implements Condition, java.io.Serializable {
    private static final long serialVersionUID = 1173984872572414699L;
    // 条件队列中第一个 node
    private transient Node firstWaiter;
    // 条件队列中最后一个 node
    private transient Node lastWaiter;
    /**
     * Creates a new {@code ConditionObject} instance.
     */
        public ConditionObject() { }
}
```

ConditionObject 是实现 Condition 接口的，Condition 接口相当于 Object 的各种监控方法， 比如 Object#wait ()、Object#notify、Object#notifyAll 这些方法，我们可以先这么理解，后 面会细说。

### 公用Node

Node 非常重要， 即是同步队列的节点， 又是条件队列的节点， 在入队的时候， 我们用 Node 把线程包装一下，然后把 Node 放入两个队列中，我们看下 Node 的数据结构，如下：

```java
static final class Node {

        /**
         * 同步队列单独的属性
         */
        //node 是共享模式
        static final Node SHARED = new Node();

        //node 是排他模式
        static final Node EXCLUSIVE = null;

        // 当前节点的前节点
        // 节点被 acquire 后就会变成head
        // head 节点不能被 cancelled
        volatile Node prev;

        // 当前节点的下一个节点
        volatile Node next;

        /**
         * 两个队列共享的属性
         */
        // 表示当前节点的状态，通过节点的状态来控制节点的行为
        // 普通同步节点，就是 0 ，条件节点是 CONDITION -2
        volatile int waitStatus;

        // waitStatus 的状态有以下几种
        static final int CANCELLED =  1;//被取消

        // 状态的意义：同步队列中的节点在自旋获取锁的时候，如果前一个节点的状态是 SIGNAL，那么自己就可以阻塞休息了，否则自己一直自旋尝试获得锁
        static final int SIGNAL    = -1;

        // 表示当前 node 正在条件队列中，当有节点从同步队列转移到条件队列时，就会被赋值成 CONDITION
        static final int CONDITION = -2;

        // 无条件传播,共享模式下，该状态的进程处于可运行状态
        static final int PROPAGATE = -3;

        // 当前节点的线程
        volatile Thread thread;

        // 在同步队列中，nextWaiter 并不真的是指向其下一个节点，我们用 next 表示同步队列的下一个节点，这只是表示当前 Node 是排他模式还是共享模式
        // 但在条件队列中，nextWaiter 就是表示下一个节点元素
        Node nextWaiter;
    }
```

从 Node 的结构中， 我们需要重点关注 **waitStatus** 字段， Node 的很多操作都是围绕着 waitStatus 字段进行的。

Node 的 pre、next 属性是同步队列中的链表前后指向字段，nextWaiter 是条件队列中下一个 节点的指向字段，但在同步队列中，nextWaiter 只是一个标识符，表示当前节点是共享还是排 它模式。

## 同步器状态

1. state 是锁的状态，是 int 类型，子类继承 AQS 时，都是要根据 state 字段来判断有无得 到锁，比如当前同步器状态是 0，表示可以获得锁，当前同步器状态是 1，表示锁已经被 其他线程持有，当前线程无法获得锁；

2. waitStatus 是节点（Node）的状态，种类很多，一共有初始化 (0)、CANCELLED (1)、 SIGNAL (-1)、CONDITION (-2)、PROPAGATE (-3)，各个状态的含义可以见上文。



## 获得锁

获取锁最直观的感受就是使用 Lock.lock () 方法来获得锁，最终目的是想让线程获得对资源的 访问权。

Lock 一般是 AQS 的子类，lock 方法根据情况一般会选择调用 AQS 的 acquire 或 tryAcquire 方法。

acquire 方法 AQS 已经实现了，tryAcquire 方法是等待子类去实现，acquire 方法制定了获取 锁的框架， 先尝试使用 tryAcquire 方法获取锁， 获取不到时， 再入同步队列中等待锁。 tryAcquire 方法 AQS 中直接抛出一个异常， 表明需要子类去实现， 子类可以根据同步器的 state 状态来决定是否能够获得锁，接下来我们详细看下 acquire 的源码解析。

acquire 也分两种，一种是排它锁，一种是共享锁，我们一一来看下：

### acquire 排它锁

```java
public final void acquire(int arg) {
        // tryAcquire方法是需要实现类去实现的，实现思路一般都是 cas 给 stats 赋值来决定是否能获得锁
        if (!tryAcquire(arg) &&
            // addWaiter 入参代表是排他模式
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
```

以上代码的主要步骤是（流程见整体架构图中红色场景）：

排他模式下，尝试获得锁

1. 如果执行一次tryAcquire就成功，直接返回，否则线程尝试进入同步队列，tryAcquire 交给子类去实现
2. addWaiter 把当前线程放到同步队列的队尾
3. acquireQueued 方法两个作用，1：阻塞当前节点，2：节点被唤醒时，使其能够获得锁
4. 如果以上步骤都失败了，打断线程释放锁
   

#### addWaiter

代码很少，每个方法都是关键，接下来我们先来看下 addWaiter 的源码实现：

```java
//	主要目的: node 追加到同步队列的队尾
    // 入参是 Node 的模式（排他模式还是共享模式）
    // 出参是新增的 node
    // 新 node.pre = 队尾
    // 队尾.next = 新 node
    private Node addWaiter(Node mode) {
        // 初始化 Node
        Node node = new Node(Thread.currentThread(), mode);
        // 这里的逻辑和 enq 一致，enq 的逻辑仅仅多了队尾是空，初始化的逻辑
        // 这个思路在java源码中很常见，先简单的尝试放一下，成功立马返回，如果不行，再while循环
        // 很多时候，这种算法可以帮忙解决大部分的问题，大部分 部分的入队可能一次都能成功，无需自旋
        Node pred = tail;
        if (pred != null) {
            node.prev = pred;
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                return node;
            }
        }
        //自旋保证node加入到队尾
        enq(node);
        return node;
    }

    // 线程加入同步队列中方法，追加到队尾
    // 这里需要重点注意的是，返回值是添加 node 的前一个节点
    private Node enq(final Node node) {
        for (;;) {
            // 得到队尾节点
            Node t = tail;
            // 如果队尾为空，说明当前同步队列都没有初始化，进行初始化
            // tail = head = new Node();
            if (t == null) {
                if (compareAndSetHead(new Node()))
                    tail = head;
                //当前节点置为队尾
            } else {
                node.prev = t;
                // node 追加到队尾
                if (compareAndSetTail(t, node)) {
                    t.next = node;
                    return t;
                }
            }
        }
    }
```

​	其中有一点值得我们学习的地方，是在 addWaiter 方法中，并没有进入方法后立马就自旋，而 是先尝试一次追加到队尾，如果失败才自旋，因为大部分操作可能一次就会成功，这种思路在我 们写自旋的时候可以借鉴。

#### acquireQueued

下一步就是要阻塞当前线程了，是 acquireQueued 方法来实现的，我们来看下源码实现：

```java
    // 主要做两件事情：
    // 1：通过不断的自旋尝试使自己前一个节点的状态变成 signal，然后阻塞自己。
    // 2：如果前一个节点获得锁，执行完成之后，再释放锁时，会把阻塞的 node 唤醒,唤醒之后再次自旋，再次无限 for 循环尝试获得锁
    // 返回false表示获得锁成功，返回true表示失败
    final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            boolean interrupted = false;
            // 自旋
            for (;;) {
                // 选上一个节点
                final Node p = node.predecessor();
                // 有两种情况会走到 if：
                // 1:node 之前没有获得锁，进入 acquireQueued 方法时，发现他的前置节点就是头节点，于是尝试获得一次锁
                // 2:node 之前一直在阻塞沉睡，然后被唤醒，此时唤醒 node 的节点正是其前置节点，也能走到 if，具体见 release 方法

                // 如果自己 tryAcquire 成功，就立马把自己设置成 head，把上一个节点移除
                // 如果 tryAcquire 失败，尝试进入同步队列
                if (p == head && tryAcquire(arg)) {
                    // 获得锁，设置成 head 节点
                    setHead(node);
                    //p被回收
                    p.next = null; // help GC
                    failed = false;
                    return interrupted;
                }

                // shouldParkAfterFailedAcquire 把node的前一个节点状态置为SIGNAL
                // 只要前一个节点状态是SIGNAL了，那么自己就可以阻塞(park)了
                // parkAndCheckInterrupt 阻塞当前线程
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    interrupted = true;
            }
        } finally {
            //如果获得node的锁失败，将node从队列中移除
            if (failed)
                cancelAcquire(node);
        }
    }
```

此方法的注释还是很清楚的， 我们接着看下此方法的核心：shouldParkAfterFailedAcquire， 这个方法的主要目的就是把前一个节点的状态置为 SIGNAL， 只要前一个节点的状态是 SIGNAL， 当前节点就可以阻塞了（parkAndCheckInterrupt 就是使节点阻塞的方法）， 源码 如下：

```java
 // 当前线程可以安心等待的标准，就是前一个节点线程状态是SIGNAL了。
    // 入参 pred 是当前节点 node 的前一个节点

    // 关键操作：
    // 1：确认前置节点是否有效，无效的话，一直往前找到状态不是取消的节点。
    // 2: 把前置节点状态置为 SIGNAL。
    // 1、2 两步操作，有可能一次就成功，有可能需要外部循环多次才能成功，但最后一定是可以成功的
    private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
        int ws = pred.waitStatus;
        // 如果前一个节点 waitStatus 状态已经是SIGNAL了，直接返回，不需要在自旋了
        if (ws == Node.SIGNAL)
            /*
             * This node has already set status asking a release
             * to signal it, so it can safely park.
             */
            return true;
        // 如果当前节点状态已经被取消了。
        if (ws > 0) {
            /*
             * Predecessor was cancelled. Skip over predecessors and
             * indicate retry.
             */
            // 找到前一个状态不是取消的节点，把当前 node 挂在有效节点身上
            // 因为节点状态是取消的话，是无效的，是不能作为 node 的前置节点的，所以必须找到 node 的有效节点才行
            do {
                node.prev = pred = pred.prev;
            } while (pred.waitStatus > 0);
            pred.next = node;
        // 否则直接把节点状态置 为SIGNAL
        } else {
            /*
             * waitStatus must be 0 or PROPAGATE.  Indicate that we
             * need a signal, but don't park yet.  Caller will need to
             * retry to make sure it cannot acquire before parking.
             */
            compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
        }
        return false;
    }
```

acquire 整个过程非常长，代码也非常多，但注释很清楚，可以一行一行仔细看看代码。

总结一下，acquire 方法大致分为三步：

1. 使用 tryAcquire 方法尝试获得锁，获得锁直接返回，获取不到锁的走 2；
2. 把当前线程组装成节点（Node），追加到同步队列的尾部（addWaiter）；
3. 自旋，使同步队列中当前节点的前置节点状态为 signal 后，然后阻塞自己。

### acquireShared 获取共享锁

acquireShared 整体流程和 acquire 相同，代码也很相似。重复的源码就不贴了，我们就贴出 来不一样的代码来，也方便进行比较：

1. 第一步尝试获得锁的地方，有所不同，排它锁使用的是 tryAcquire 方法，共享锁使用的 是 tryAcquireShared 方法，如下图：

   ![image-20230218162522501](image-20230218162522501.png)

2. 第二步不同， 在于节点获得排它锁时， 仅仅把自己设置为同步队列的头节点即可 （setHead 方法），但如果是共享锁的话，还会去唤醒自己的后续节点，一起来获得该锁 （setHeadAndPropagate 方法），不同之处如下：

   ![image-20230218162646567](image-20230218162646567.png)

接下来我们一起来看下 setHeadAndPropagate 方法的源码：

```java
    // 主要做两件事情
    // 1:把当前节点设置成头节点
    // 2:看看后续节点有无正在等待，并且也是共享模式的，有的话唤醒这些节点
    private void setHeadAndPropagate(Node node, int propagate) {
        Node h = head; // Record old head for check below
        setHead(node);
        /*
         * Try to signal next queued node if:
         *   Propagation was indicated(表示指示) by caller,
         *     or was recorded (as h.waitStatus either before
         *     or after setHead) by a previous operation
         *     (note: this uses sign-check of waitStatus because
         *      PROPAGATE status may transition to SIGNAL.)
         * and
         *   The next node is waiting in shared mode,
         *     or we don't know, because it appears null
         *
         * The conservatism(保守) in both of these checks may cause
         * unnecessary wake-ups, but only when there are multiple
         * racing acquires/releases, so most need signals now or soon
         * anyway.
         */
        // propagate > 0 表示已经有节点获得共享锁了
        if (propagate > 0 || h == null || h.waitStatus < 0 ||
            (h = head) == null || h.waitStatus < 0) {
            Node s = node.next;
            //共享模式，还唤醒头节点的后置节点
            if (s == null || s.isShared())
                doReleaseShared();
        }
    }
```

这个就是共享锁独特的地方，当一个线程获得锁后，它就会去唤醒排在它后面的其它节点，让其 它节点也能够获得锁。

