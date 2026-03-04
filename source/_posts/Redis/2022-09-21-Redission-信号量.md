---
title: Redission 信号量
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-09-21 10:43:33
subtitle:
tags:
  - Redis
  - 分布式
categories:
  -	Redis
---

## 前言

>信号量大家应该够不陌生，比如操作系统中的pv信号量，依据功能还可以分成同步信号量、互斥信号量。由于redis是单进程，天生具有锁的这个特性，而redisson更是对redis做了一层包装，使得api接口更适用。今天我们就看一下redisson基于redis做的信号量。

## Redisson信号量

​	Redisson的分布式信号量（Semaphore）Java对象 RSemaphore 采用了与 java.util.concurrent.Semaphore 相似的接口和用法。同时还提供了**异步（Async）、反射式（Reactive）和RxJava2标准的接口**。

### 常用API

```java
RSemaphore semaphore = redisson.getSemaphore("semaphore");
semaphore.acquire();
//或
semaphore.acquireAsync();
semaphore.acquire(23);
semaphore.tryAcquire();
//或
semaphore.tryAcquireAsync();
semaphore.tryAcquire(23, TimeUnit.SECONDS);
//或
semaphore.tryAcquireAsync(23, TimeUnit.SECONDS);
semaphore.release(10);
semaphore.release();
//或
semaphore.releaseAsync();
```

### 实践

#### 1、限流

```java
/**
 * 信号量可以做分布式限流
 *
 * @return
 * @throws InterruptedException
 */
@GetMapping("/park")
@ResponseBody
public String park() throws InterruptedException {

    RSemaphore park = redisson.getSemaphore("park");
    park.acquire();//获取一个信号量（redis中信号量值-1）,如果redis中信号量为0了，则在这里阻塞住，直到信号量大于0，可以拿到信号量，才会继续执行。
    
  	//boolean b = park.tryAcquire();//这个就是不阻塞，能拿到信号量就为true，拿不到（信号量为0 了）就是false

    //业务代码...
    return "ok" + b;

}

@GetMapping("/go")
@ResponseBody
public String go() throws InterruptedException {
    RSemaphore park = redisson.getSemaphore("park");
    park.release();//释放一个信号量（redis中信号量值+1）
    return "走了";

}
```



#### 2、商品秒杀

1、商品上架设置，设置信号量控制商品可以秒杀的数目

```java
 //5、使用库存作为分布式的信号量  限流；
 	RSemaphore semaphore = redissonClient.getSemaphore(SKU_STOCK_SEMAPHORE + token);
//商品可以秒杀的数量作为信号量
	semaphore.trySetPermits(seckillSkuVo.getSeckillCount());
//TODO 设置过期时间。
	semaphore.expireAt(sesssion.getEndTime());
```

2、秒杀工作

```java
 //验证这个人是否已经购买过。幂等性; 如果只要秒杀成功，就去占位。  userId_SessionId_skuId
	//SETNX
   String redisKey = respVo.getId() + "_" + skuId;
   //自动过期
   Boolean aBoolean = redisTemplate.opsForValue().setIfAbsent(redisKey, num.toString(), ttl, TimeUnit.MILLISECONDS);
   if (aBoolean) {
    	//占位成功说明从来没有买过
       RSemaphore semaphore = redissonClient.getSemaphore(SKU_STOCK_SEMAPHORE + randomCode);
       //120  20ms
       boolean b = semaphore.tryAcquire(num);
       if (b) {
        //秒杀成功;
        //快速下单。发送MQ消息  10ms
         ...
       }
   }
```

