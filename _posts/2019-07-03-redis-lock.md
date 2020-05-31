---
layout: single
title: "Redis - Distributed Locks for Transactions"
author: "Danni"
toc: true
toc_sticky: true
tags:
  - Distributed System
---
> Client Handeling, access control & performance metrics

Redis is an open source, `in-memory` data structure store. Most people know it as database but it can also be used as cache and message broker. 

Redis supports 5 data types:
- **String**: most common Redis value. Binary safe so can contain any data.
- **List**: simply list of strings, can be updated on both sides. Operations like add, remove, and check are in O(1).
- **Sets**: an unsorted collection of strings.
- **Hash**: maps between string fileds and string value.
- **Sorted set**: similar to set but is associated with score for ordering sets from the smallest to greatest.

> Redis also supports Bitmaps and Hyperlogs but these are actually based on strings.

## Use Cases

Imagine 11.11 or Amazon Prime day, you want to buy a 80% off alienware while there are maybe thousands of people want to do the same. How can the platform control the resource management when there are a huge amount of users competing for the same product? That is, multiple processes operating towards the resource. High concurreny & Efficiency is what we be looking for.

## Some naive thoughts

According to what we learnt in the OS class, resource control can be realized with adding `synchronized` to the whole method so that it can be locked. Or, we can just lock the code that are doing the database operations. But can we do better? 

Remember `Thread pool` in Java, it uses a unbounded queue for operations so that there are no concurrent problems. So this could also be a way to manage threads.

But, simply adding locks cannot guarantee threads competing for different products will not competing over the same lock. So to ensure that different processes in many environment must operate with shared resources in a mutually exclusive way, Redis introduces `Distributed Locks`.

## Distributed Lock

> A more specific senario here:
> There's a table in database with two columns: product ID and amount. When a user buys a product, the amount will be automatically -1. So if there are 500 processes competing over product A and other 500 processes competing over product B, we need a way to guarante the mutual exclusion. 

## Demo

#### Cache Lock

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface CacheLock {
    String lockedPrefix() default "";//prefix of key lock
    long timeOut() default 2000;
    int expireTime() default 1000;// Period that the lock exists
}
```

#### How to lock & unlock?

```java
/**
     * How to add a lock:
     * lock();
     * try{
     *    executeMethod();
     * }finally{
     *   unlock();
     * }
     * @param timeout
     * @param expire
     * @return success or fail
     */
    public boolean lock(long timeout,int expire){
        long nanoTime = System.nanoTime();
        timeout *= MILLI_NANO_TIME;
        try {
            while (System.nanoTime() - nanoTime < timeout) {
                // if not exists, set lock
                if (this.redisClient.setnx(this.key, LOCKED) == 1) {
                    this.redisClient.expire(key, expire);// expire time avoid congestion
                    this.lock = true;
                    return this.lock;
                }
                System.out.println("waiting");
                // temporary sleep to avoid live lock
                Thread.sleep(3, RANDOM.nextInt(30));
            } 
        } catch (Exception e) {
            throw new RuntimeException("locking error",e);
        }
        return false;
    }

    public void unlock() {
        try {
            if(this.lock){
                redisClient.delKey(key);
            }
        } catch (Throwable e) {

        }
    }
```

#### Test

> This test case took reference [here](https://blog.csdn.net/u010359884/article/details/50310387).
Two `for` loops are the two respective products. The main process use `countDown()` to make sure all 1000 excuted at the same time.

```java
@Test
    public void testSecKill(){
        int threadCount = 1000;
        int splitPoint = 500;
        CountDownLatch endCount = new CountDownLatch(threadCount);
        CountDownLatch beginCount = new CountDownLatch(1);
        SecKillImpl testClass = new SecKillImpl();

        Thread[] threads = new Thread[threadCount];
        for(int i= 0;i < splitPoint;i++){
            threads[i] = new Thread(new  Runnable() {
                public void run() {
                    try {
                        beginCount.await();
                        SeckillInterface proxy = (SeckillInterface) Proxy.newProxyInstance(SeckillInterface.class.getClassLoader(),
                            new Class[]{SeckillInterface.class}, new CacheLockInterceptor(testClass));
                        proxy.secKill("test", commidityId1);
                        endCount.countDown();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
            threads[i].start();

        }
        for(int i= splitPoint;i < threadCount;i++){
            threads[i] = new Thread(new  Runnable() {
                public void run() {
                    try {
                        beginCount.await();
                        SeckillInterface proxy = (SeckillInterface) Proxy.newProxyInstance(SeckillInterface.class.getClassLoader(), 
                            new Class[]{SeckillInterface.class}, new CacheLockInterceptor(testClass));
                        proxy.secKill("test", commidityId2);
                        endCount.countDown();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
            threads[i].start();

        }
        long startTime = System.currentTimeMillis();
        beginCount.countDown();

        try {
            // 
            endCount.await();
            System.out.println(SecKillImpl.inventory.get(commidityId1));
            System.out.println(SecKillImpl.inventory.get(commidityId2));
            System.out.println("error count" + CacheLockInterceptor.ERROR_COUNT);
            System.out.println("total cost " + (System.currentTimeMillis() - startTime));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```

## Top Performance Metrics

1. **Throughput**: the pattern of load on a server(peak load, the frequency of peak load, average load etc.)
2. **Memory Utilization**: Used memory defines total number of bytes allocated by Redis using its allocator (either standard libc, jemalloc, or an alternative allocator such as tcmalloc).

```shell
>> info memory
# Memory
used_memory: ***
used_memory_human: ***K
used_memory_rss:*******
used_memory_rss_human: ****M
used_memory_peak: *****
used_memory_peak_human: *****K
```
3. **Cache Hit Ratio**: the efficiency of cache usage. <u>CHR = (Total Key Hits)/(Total Key Hits + Total Key Misses)</u>. If the cache hit ratio is lower than `~ 0.8` then a significant amount of the requested keys are evicted, expired, or do not exist at all.
4. **Active Connections**: ensure having sufficient connections to serve all your requests at peak time.
5. **Evicted/Expired Keys**: A persistent positive value of this metric is an indication that you need to scale the memory up.
6. **Replication Metrics**: `connected_slaves` says the reachability of the slave server to master server. The unreachability can lead to higher read latency (depending on the read load of a server).

```shell
>> info replication
# Replication
role:master/slave
connected_slaves:0/master_slave_io_seconds_ago:0
master_repl_offset:0
```


## References
[1] !(Distributed locks with Redis)https://redis.io/topics/distlock
[2] !(Data Types)https://redis.io/topics/data-types