---
title: 线程状态Blocked和Waiting
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2023-02-13 19:00:21
subtitle:
tags:
  -	多线程
categories:
  - 多线程
---

![image-20230213190241261](image-20230213190241261.png)

入口区:	等待获取锁的线程状态为Blocked，获取锁失败，然后线程就排队等待

等待区:	等待被唤醒的线程状态为Waiting(Time_Waiting)，线程在获取锁后调用自身的wait()方法，然后释放锁，进入等待区等待

Java线程状态中,有blocked和wating状态，都是阻塞态 但阻塞的原因有所不同，对于等待取，线程A已经获得锁，其它线程 在尝试拿锁时，就会阻塞，在java中就是实用synchronized关键字 进行加锁，失败就会阻塞进入entrySet。此时线程状态就是Blocked状态。

​	如果线程A调用了wait()方法，那么就会释放锁，自己进入waitSet，此时线程状态就是waiting状态。

还有需要注意的是，某个线程B想要获得对象锁，一般情况下有两个先决条件，一是对象锁已经被释放了（如曾经持有锁的前任线程A执行完了synchronized代码块或者调用了wait()方法等等），二是线程B已处于RUNNABLE状态。

​	那么这两类集合中的线程都是在什么条件下可以转变为RUNNABLE呢？

​	对于Entry Set中的线程，当对象锁被释放的时候，JVM会唤醒处于Entry Set中的某一个线程，这个线程的状态就从BLOCKED转变为RUNNABLE。

​	对于Wait Set中的线程，当对象的notify()方法被调用时，JVM会唤醒处于Wait Set中的某一个线程，这个线程的状态就从WAITING转变为RUNNABLE；或者当notifyAll()方法被调用时，Wait Set中的全部线程会转变为RUNNABLE状态。所有Wait Set中被唤醒的线程会被转移到Entry Set中

![Protocol state machine example - Thread states and life cycle in Java.](state-machine-example-java-6-thread-states.png)

>参考:https://www.uml-diagrams.org/java-thread-uml-state-machine-diagram-example.html
