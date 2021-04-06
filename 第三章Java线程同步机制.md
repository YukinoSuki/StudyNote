# 锁的概述

将多个线程对共享数据的并发访问转换为串行访问，即一个共享数据一次只能被一个线程访问，锁就是实现这种思路而诞生的同步机制。

一个线程访问共享数据前必须要 **获得锁** ，一个线程获得某个锁，我们就称该线程为持有该锁的线程，**一个锁一次只能被一个线程持有**。访问锁结束之后要 **释放锁**。获得锁之后至释放锁之后执行的代码被称为**临界区**。

按照Java虚拟机对锁实现方式的划分，Java平台的锁包括**内部锁（Intrinsic Lock）**和**显式锁（Explicit Lock）**。内部锁是通过synchronized关键字实现的；显式锁是通过java.conrrent.locks.Lock接口的实现类实现的。
  
    
# 内部锁：synchronized关键字

synchronized 关键字修饰的方法被称为同步方法。修饰的静态方法被称为同步静态方法，修饰的实例方法就被称为同步实例方法，同步的整个方法体就是一个临界区。

synchronized修饰的代码块称为同步代码块，语法如下，其中锁句柄是一个对象的引用。

```Java
    synchronized(锁句柄){
        //在此代码块中访问共享数据
    }
```


# 显式锁：Lock接口

显式锁是java.util.concurrent.locks.Lock接口的实例。java.util.concurrent.locks.ReentrantLock是Lock接口的默认实现类。


## 方法摘要
* void lock() 获取锁。
* void lockInterruptibly() 如果当前线程未被中断，则胡；哦去锁。
* Condition newCondition() 返回绑定到此Lock实例的新Condition实例。
* boolean tryLock() 仅在调用时锁为空闲状态才获取该锁
* boolean tryLock(long time, TimeUnit unit) 如果锁在给定的等待时间为空闲，并且当前线程未被终端，则获取锁。
* void unlock() 释放锁



## 显式锁的使用方法如下：

```Java
    private Lock lock = ... //创建一个Lock接口实例
    ...
    lock.lock(); //申请锁
    try{
        // 操作共享数据
    }finally{
        lock.unlock();//释放锁
    }

```

# 锁的适用场景

* check-then-act操作：
* read-modify-wirte操作：
* 多个线程对多个共享数据进行更新：
  

# 线程同步机制的底层助手：内存屏障  

在Java平台中，锁的获取隐含着**刷新处理器缓存**这个动作，意味着持有锁的线程会对共享变量所做的更新同步到该线程执行处理器的**高速缓存**中；而相应的锁的释放隐含着**冲刷处理器混存**这个动作，意味着写线程会把对共享变量所做的更新能够被推送到**高速缓存**中，从而对读线程可同步。

按照可见性来划分，内存屏障可分为**加载屏障（Load Barrier）**和**存储屏障（Store Barrier）**。加载屏障的作用是刷新处理器缓存，存储屏障的作用是冲刷处理器缓存。Java虚拟机会在Monitor Exit（释放锁）之后插入一个存储屏障，保障了释放锁之前所做的操作能够被读线程同步读取到。

按照有序性保障来划分，内存屏障可以分为**获取屏障（Acquire Barrier）**和**释放屏障（Release Barrier）**。获取屏障是在一个读操作之后插入该内存屏障，起作用是禁止该读操作与之后的任何读写操作之间进行**重排序**。释放屏障的适用方式是在一个写操作之前插入该内存屏障，其作用是防止该写操作与之前的任何读写产生重排序。

# 轻量级同步机制: volatile关键字




