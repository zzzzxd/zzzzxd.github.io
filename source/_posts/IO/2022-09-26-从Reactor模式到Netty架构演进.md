---
title: 从Reactor模式到Netty架构演进
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-09-26 19:05:27
subtitle:
tags:
  - IO
categories:
  - IO
---

## 单线程Reactor

### Reactor模型的朴素原型

之前的文章探讨过IO模型，其中最优化的是基于多路复用器的NIO。Java的NIO模式的Selector网络通讯，其实就是一个简单的Reactor模型。可以说是Reactor模型的朴素原型。

我们再来回顾一下代码

```java

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Set;
public class SocketMultiplexingSingleThreadv1 {
    private ServerSocketChannel server = null;
    private Selector selector = null;   //linux 多路复用器（select poll    epoll kqueue） nginx  event{}
    int port = 9090;
    public void initServer() {
        try {
            server = ServerSocketChannel.open();
            server.configureBlocking(false);
            server.bind(new InetSocketAddress(port));
            server.register(selector, SelectionKey.OP_ACCEPT);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void start() {
        initServer();
        System.out.println("服务器启动了。。。。。");
        try {
            while (true) {
                Set<SelectionKey> keys = selector.keys();
                System.out.println(keys.size()+"   size");
                while (selector.select() > 0) {
                    Set<SelectionKey> selectionKeys = selector.selectedKeys();  //返回的有状态的fd集合
                    Iterator<SelectionKey> iter = selectionKeys.iterator();
              
                    while (iter.hasNext()) {
                        SelectionKey key = iter.next();
                        iter.remove(); //set  不移除会重复循环处理
                        if (key.isAcceptable()) {       
                            acceptHandler(key);
                        } else if (key.isReadable()) {
                            readHandler(key);           
                        }
                    }
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void acceptHandler(SelectionKey key) {
        try {
            ServerSocketChannel ssc = (ServerSocketChannel) key.channel();
            SocketChannel client = ssc.accept(); //来啦，目的是调用accept接受客户端  fd7
            client.configureBlocking(false);
            ByteBuffer buffer = ByteBuffer.allocate(8192);
            client.register(selector, SelectionKey.OP_READ, buffer);
            System.out.println("-------------------------------------------");
            System.out.println("新客户端：" + client.getRemoteAddress());
            final String[] split = client.getRemoteAddress().toString().split(":");
            if (split[1].equals("15098"))
                System.out.println(System.currentTimeMillis()/1000);
            System.out.println("-------------------------------------------");

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void readHandler(SelectionKey key) {
        SocketChannel client = (SocketChannel) key.channel();
        ByteBuffer buffer = (ByteBuffer) key.attachment();
        buffer.clear();
        int read = 0;
        try {
            while (true) {
                read = client.read(buffer);
                if (read > 0) {
                    buffer.flip();
                    while (buffer.hasRemaining()) {
                        client.write(buffer);
                    }
                    buffer.clear();
                } else if (read == 0) {
                    break;
                } else {
                    client.close();
                    break;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();

        }
    }

    public static void main(String[] args) {
        SocketMultiplexingSingleThreadv1 service = new SocketMultiplexingSingleThreadv1();
        System.out.println(System.currentTimeMillis()/1000);
        service.start();
    }
}
```

### 单线程Reactor

实际上的Reactor模式，是基于Java NIO的，在他的基础上，抽象出来两个组件——Reactor和Handler两个组件：

（1）Reactor：负责响应IO事件，当检测到一个新的事件，将其发送给相应的Handler去处理；新的事件包含连接建立就绪、读就绪、写就绪等。

（2）Handler:将自身（handler）与事件绑定，负责事件的处理，完成channel的读入，完成处理业务逻辑后，负责将结果写出channel。

如下图所示:

![wpsC334.tmp](1.jpg)

这是最简单的单Reactor单线程模型。Reactor线程是个多面手，负责多路分离套接字，Accept新连接，并分派请求到Handler处理器中。

下面的图，来自于“Scalable IO in Java”，和上面的图的意思，差不多。Reactor和Hander 处于一条线程执行。

![wpsC345.tmp](2.jpg)

顺便说一下，可以将上图的accepter，看做是一种特殊的handler。

## 多线程Reactor

​	上述的单线程Reactor虽然能使用单线程应对多个并发，但是还没有发挥出多线程的优势，现代的cpu都是多核cpu，如果只是单线程 明显发挥不出多核cpu的实力。我们尝试改造Reactor模式为多线程。
​	很容易想到，用一个主线程（reactor）来接收连接请求，具体的read、wirte等操作，拿一个线程池来去干活，这就可以发挥发线程的优势。

下面的图，来自于“Scalable IO in Java”,Reactor是一条独立的线程，Hander 处于线程池中执行。

![wpsC376.tmp](5.jpg)

​	改造代码如下:

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Iterator;
import java.util.Set;

public class SocketMultiplexingSingleThreadv2 {

    private ServerSocketChannel server = null;
    private Selector selector = null;   //linux 多路复用器（select poll epoll） nginx  event{}
    int port = 9090;
    public void initServer() {
        try {
            server = ServerSocketChannel.open();
            server.configureBlocking(false);
            server.bind(new InetSocketAddress(port));
            selector = Selector.open();  //  select  poll  *epoll
            server.register(selector, SelectionKey.OP_ACCEPT);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public void start() {
        initServer();
        System.out.println("服务器启动了。。。。。");
        try {
            while (true) {
                while (selector.select(50) > 0) {
                    Set<SelectionKey> selectionKeys = selector.selectedKeys();
                    Iterator<SelectionKey> iter = selectionKeys.iterator();
                    while (iter.hasNext()) {
                        SelectionKey key = iter.next();
                        iter.remove();
                        if (key.isAcceptable()) {
                            acceptHandler(key);
                        } else if (key.isReadable()) {
                            System.out.println("in.....");
                            readHandler(key);//非阻塞  即便以抛出了线程去读取，但是在时差里，这个key的read事件会被重复触发

                        } else if(key.isWritable()){                    
                            key.interestOps(key.interestOps() & ~SelectionKey.OP_WRITE);
                            writeHandler(key);
                        }
                    }
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private void writeHandler(SelectionKey key) {
        new Thread(()->{
            System.out.println("write handler...");
            SocketChannel client = (SocketChannel) key.channel();
            ByteBuffer buffer = (ByteBuffer) key.attachment();
            buffer.flip();
            while (buffer.hasRemaining()) {
                try {

                    client.write(buffer);
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            buffer.clear();
        }).start();

    }

    public void acceptHandler(SelectionKey key) {
        try {
            ServerSocketChannel ssc = (ServerSocketChannel) key.channel();
            SocketChannel client = ssc.accept();
            client.configureBlocking(false);
            ByteBuffer buffer = ByteBuffer.allocate(8192);
            client.register(selector, SelectionKey.OP_READ, buffer);
            System.out.println("-------------------------------------------");
            System.out.println("新客户端：" + client.getRemoteAddress());
            System.out.println("-------------------------------------------");

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public void readHandler(SelectionKey key) {
        new Thread(()->{
            System.out.println("read handler.....");
            SocketChannel client = (SocketChannel) key.channel();
            ByteBuffer buffer = (ByteBuffer) key.attachment();
            buffer.clear();
            int read = 0;
            try {
                while (true) {
                    read = client.read(buffer);
                    System.out.println(Thread.currentThread().getName()+ " " + read);
                    if (read > 0) {
                        key.interestOps(  SelectionKey.OP_READ);

                        client.register(key.selector(),SelectionKey.OP_WRITE,buffer);
                    } else if (read == 0) {
                        break;
                    } else {
                        client.close();
                        break;
                    }
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }).start();

    }

    public static void main(String[] args) {
        SocketMultiplexingSingleThreadv2 service = new SocketMultiplexingSingleThreadv2();
        service.start();
    }
}

```

这个乍一看好像没啥问题，实际运行的话是有点问题的，我们运行之后，在客户端处输入一个hello

![image-20220926220333005](3.jpg) ![image-20220926220418249](4.jpg)

看到什么问题？我明明在客户端只输入了一次hello，但是确重复调用了，具体原因在代码中注释了,就是因为在切换线程处理的时候，这个空隙中 由于缓冲区没有读完，相当于read事件没有被处理完成，所以会一直调用。也就是说，每次输入，只要切换线程，这个背后可能就会调用多次epoll_wait得到read 事件。<font color=red>也就是会造成重复的系统调用</font>。

​	这样这么多浪费的系统调用被调肯定不行，并发多的时候 一定会影响性能，但我们还想利用多线程怎么办呢？

实际这个问题就是在于主线程非阻塞造成的，这一个reactor线程 判断获取了read事件、write事件 却切换了线程处理,如果我们让一个线程对此selector注册的事件负责到底，判断是read事件则进行read处理，判断是write事件就进行write处理，大不了多来几个reactor不就可以了吗？思路转，天地宽。

优化代码:

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Set;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.atomic.AtomicInteger;

public class SocketMultiplexingThreads {

    private ServerSocketChannel server = null;
    private  Selector selector1 = null;
    private  Selector selector2 = null;
    private  Selector selector3 = null;
    int port = 9090;

    public void initServer() {
        try {
            server = ServerSocketChannel.open();
            server.configureBlocking(false);
            server.bind(new InetSocketAddress(port));
            selector1 = Selector.open();
            selector2 = Selector.open();
            selector3 = Selector.open();
            server.register(selector1, SelectionKey.OP_ACCEPT);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SocketMultiplexingThreads service = new SocketMultiplexingThreads();
        service.initServer();
        NioThread T1 = new NioThread(service.selector1 ,2);
        NioThread T2 = new NioThread(service.selector2);
        NioThread T3 = new NioThread(service.selector3);

        T1.start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        T2.start();
        T3.start();

        System.out.println("服务器启动了。。。。。");

        try {
            System.in.read();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}

class NioThread extends Thread {
     Selector selector = null;
     static int selectors = 0;
     int id = 0;
     volatile static BlockingQueue<SocketChannel>[] queue;
     static AtomicInteger idx = new AtomicInteger();

    NioThread(Selector sel,int n ) {
        this.selector = sel;
        this.selectors =  n;

        queue =new LinkedBlockingQueue[selectors];
        for (int i = 0; i < n; i++) {
            queue[i] = new LinkedBlockingQueue<>();
        }
        System.out.println("Boss 启动");
    }
   NioThread(Selector sel  ) {
        this.selector = sel;
       id = idx.getAndIncrement() % selectors  ;
       System.out.println("worker: "+id +" 启动");
    }

    @Override
    public void run() {
        try {
            while (true) {

                while (selector.select(10) > 0) {
                    Set<SelectionKey> selectionKeys = selector.selectedKeys();
                    Iterator<SelectionKey> iter = selectionKeys.iterator();
                    while (iter.hasNext()) {
                        SelectionKey key = iter.next();
                        iter.remove();
                        if (key.isAcceptable()) {
                            acceptHandler(key);
                        } else if (key.isReadable()) {
                            readHandler(key);
                        }
                    }
                }
                if( ! queue[id].isEmpty()) {
                    ByteBuffer buffer = ByteBuffer.allocate(8192);
                    SocketChannel client = queue[id].take();
                    client.register(selector, SelectionKey.OP_READ, buffer);
                    System.out.println("-------------------------------------------");
                    System.out.println("新客户端：" + client.socket().getPort()+"分配到："+ (id));
                    System.out.println("-------------------------------------------");
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public void acceptHandler(SelectionKey key) {
        try {
            ServerSocketChannel ssc = (ServerSocketChannel) key.channel();
            SocketChannel client = ssc.accept();
            client.configureBlocking(false);
            int num = idx.getAndIncrement() % selectors;

            queue[num].add(client);

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void readHandler(SelectionKey key) {
        SocketChannel client = (SocketChannel) key.channel();
        ByteBuffer buffer = (ByteBuffer) key.attachment();
        buffer.clear();
        int read = 0;
        try {
            while (true) {
                read = client.read(buffer);
                if (read > 0) {
                    buffer.flip();
                    while (buffer.hasRemaining()) {
                        client.write(buffer);
                    }
                    buffer.clear();
                } else if (read == 0) {
                    break;
                } else {
                    client.close();
                    break;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

整体架构大致如下:

![image-20220926223327829](6.jpg)

​	我们用一个线程持有一个selector,在selector上只注册了accept事件,我们将其称为Boss,而我们再启动另外两个线程，每个线程持有一个selector，我们称其为worker线程，worker线程启动之后就负责对注册到本线程selector上的事件进行处理。

​	Boss线程接收客户端连接之后，我们直接将它注册到worker线程上selector，但是注意，直接注册由于worker调用select方法可能会阻塞，是注册不上的，此时需要唤醒操作。更好的处理方式就是代码中的通过队列解偶，让worker自己去注册即可，这就完成了一种多线程下的reactor模型。

## Netty IO

### Boss、Worker

​	多线程的reactor模式，其实重点就一个，就是让单线程持有一个selector，并且由它自己为注册到selector上的事件负责！那么，也就是说，我们将上图的Boss 变为多个线程，Worker变为更多的线程，并且Boss 线程或许也可以注册读写事件 由此来分担压力，或者 某些Worker专门注册写事件，某些Worker专门注册读事件，只要能充分利用多核cpu，可以任意组合。

 架构图:

​	![image-20220926225317405](7.jpg)

### 简单的Netty实现

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.StandardSocketOptions;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Iterator;
import java.util.Set;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.Executor;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;

public class SocketMultiplexingThreadsV2 {
    public static void main(String[] args) throws IOException {

        EventLoopGroup boss = new EventLoopGroup(1);
        EventLoopGroup worker = new EventLoopGroup(3);
        ServerBootStrap b = new ServerBootStrap();
        b.group(boss, worker).bind(9090);

        System.in.read();
    }
}

class ServerBootStrap {
    private EventLoopGroup group;
    private EventLoopGroup chiledGroup;
    ServerAcceptr sAcceptr;

    public ServerBootStrap group(EventLoopGroup boss, EventLoopGroup worker) {
        group = boss;
        chiledGroup = worker;
        return this;
    }

    public void bind(int port) throws IOException {
        //bind 处理的是server的启动过程
        ServerSocketChannel server = ServerSocketChannel.open();
        server.configureBlocking(false);
        server.bind(new InetSocketAddress(port));
        sAcceptr = new ServerAcceptr(chiledGroup, server);
        EventLoop eventloop = group.chosser();
        //把启动server，bind端口的操作变成task，推送到eventloop中执行。
        eventloop.execute(new Runnable() {
            @Override
            public void run() {
                eventloop.execute(new Runnable() {
                    @Override
                    public void run() {
                        try {
                            eventloop.name = Thread.currentThread() + eventloop.name;
                            System.out.println("bind...server...to " + eventloop.name);
                            server.register(eventloop.selector, SelectionKey.OP_ACCEPT, sAcceptr);
                        } catch (ClosedChannelException e) {
                            e.printStackTrace();
                        }
                    }
                });
            }
        });
    }
}

class EventLoopGroup {
    AtomicInteger cid = new AtomicInteger(0);
    EventLoop[] childrens = null;

    EventLoopGroup(int nThreads) {
        childrens = new EventLoop[nThreads];
        for (int i = 0; i < nThreads; i++) {
            childrens[i] = new EventLoop("T" + i);
        }
    }

    public EventLoop chosser() {
        return childrens[cid.getAndIncrement() % childrens.length];
    }
}

interface Handler {
    void doRead();
}
class ClientReader implements Handler {

    SocketChannel key;
    ClientReader(SocketChannel server) {
        this.key = server;
    }
    @Override
    public void doRead() {
        ByteBuffer data = ByteBuffer.allocateDirect(4096);
        try {
            key.read(data);
            data.flip();
            byte[] dd = new byte[data.limit()];
            data.get(dd);
            System.out.println(new String(dd));
            data.clear();
            for (int i = 0; i < 10; i++) {
                data.put("a".getBytes());
                data.flip();
                key.write(data);
                data.clear();

            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

class ServerAcceptr implements Handler {
    ServerSocketChannel key;
    EventLoopGroup cGroup;

    ServerAcceptr(EventLoopGroup cGroup, ServerSocketChannel server) {
        this.key = server;
        this.cGroup = cGroup;
    }

    public void doRead() {
        try {
            final EventLoop eventLoop = cGroup.chosser();
            final SocketChannel client = key.accept();
            client.configureBlocking(false);
            client.setOption(StandardSocketOptions.TCP_NODELAY, true);
            final ClientReader cHandler = new ClientReader(client);
            eventLoop.execute(new Runnable() {
                @Override
                public void run() {
                    try {

                        System.out.println("socket...send...to " + eventLoop.name+ " client port : " + client.socket().getPort());

                        client.register(eventLoop.selector, SelectionKey.OP_READ, cHandler);
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            });
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

class EventLoop implements Executor {

    Selector selector;
    Thread thread = null;
    BlockingQueue events = new LinkedBlockingQueue();
    int NOT_STARTED = 1;
    int STARTED = 2;
    AtomicInteger STAT = new AtomicInteger(1);
    String name;


    public EventLoop(String name) {
        try {
            this.name = name;
            selector = Selector.open();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //Loop 需要一个死循环  这个死循环在哪里运行呢？当然在一个线程里，那，那个线程怎么出现的呢？？？当然是execute创建出来的。
    public void run() throws InterruptedException, IOException {

        System.out.println("server已经开始：");

        for (; ; ) {
            //select
            int nums = selector.select();
            //selectedkeys to events
            if (nums > 0) {
                Set<SelectionKey> keys = selector.selectedKeys();  //会一直阻塞，不过可以通过外界有task到达来wakeup唤醒
                Iterator<SelectionKey> iter = keys.iterator();
                while (iter.hasNext()) {
                    SelectionKey key = iter.next();
                    iter.remove();
                    Handler handler = (Handler) key.attachment();
                    if (handler instanceof ServerAcceptr) {
                    } else if (handler instanceof ClientReader) {
                    }
                    handler.doRead();
                }
            }
            //run events
            runrTask();
        }
    }

    //线程池需要持有线程和消息队列
    @Override
    public void execute(Runnable task) {
        try {
            events.put(task);
            this.selector.wakeup();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        if (!inEventLoop() && STAT.incrementAndGet() == STARTED) {

            new Thread(new Runnable() {
                @Override
                public void run() {
                    try {
                        thread = Thread.currentThread();
                        EventLoop.this.run();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }).start();
        }
    }

    public void runrTask() throws InterruptedException {
        for (int i = 0; i < 5; i++) {
            Runnable task = (Runnable) events.poll(10, TimeUnit.MILLISECONDS);
            if (task != null) {
                events.remove(task);
                task.run();
            }
        }
    }

    private boolean inEventLoop() {
        return thread == Thread.currentThread();
    }
}
```

### 架构图

![image-20220926230127195](8.jpg)

> 参考:https://www.cnblogs.com/crazymakercircle/p/9833847.html
