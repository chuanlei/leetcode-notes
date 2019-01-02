# Java基础

[参考资料](https://github.com/crossoverJie/JCSprout)

## 多线程

### 通讯方式

### 线程状态

[jvmti参考资料](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html)

![thread-state](https://github.com/chuanlei/tech-notes/blob/master/pics/thread-state.jpg)

含义的解释如下

|Constant	|	Description|
|----------|--------------------|
|JVMTI_THREAD_STATE_ALIVE	|	Thread is alive. Zero if thread is new (not started) or terminated.|
|JVMTI_THREAD_STATE_TERMINATED	|	Thread has completed execution.|
|JVMTI_THREAD_STATE_RUNNABLE	|	Thread is runnable.|
|JVMTI_THREAD_STATE_BLOCKED_ON_MONITOR_ENTER	|	Thread is waiting to enter a synchronization block/method or, after an Object.wait(), waiting to re-enter a synchronization block/method.|
|JVMTI_THREAD_STATE_WAITING	|	Thread is waiting.|
|JVMTI_THREAD_STATE_WAITING_INDEFINITELY	|	Thread is waiting without a timeout. For example, Object.wait().|
|JVMTI_THREAD_STATE_WAITING_WITH_TIMEOUT	|	Thread is waiting with a maximum time to wait specified. For example, Object.wait(long).|
|JVMTI_THREAD_STATE_SLEEPING	|	Thread is sleeping -- Thread.sleep(long).|
|JVMTI_THREAD_STATE_IN_OBJECT_WAIT	|	Thread is waiting on an object monitor -- Object.wait.|
|JVMTI_THREAD_STATE_PARKED	|	Thread is parked, for example: LockSupport.park, LockSupport.parkUtil and LockSupport.parkNanos.|
|JVMTI_THREAD_STATE_SUSPENDED	|	Thread suspended. java.lang.Thread.suspend() or a JVM TI suspend function (such as SuspendThread) has been called on the thread. If this bit is set, the other bits refer to the thread state before suspension.|
|JVMTI_THREAD_STATE_INTERRUPTED	|	Thread has been interrupted.|
|JVMTI_THREAD_STATE_IN_NATIVE	|	Thread is in native code--that is, a native method is running which has not called back into the VM or Java programming language code.This flag is not set when running VM compiled Java programming language code nor is it set when running VM code or VM support code. Native VM interface functions, such as JNI and JVM TI functions, may be implemented as VM code.|

BLOCKED与WAITING有什么区别

### volatile关键字

### 锁（悲观、乐观）

### 中止线程

### wait()与sleep()的区别

### 线程同步有几种方法

### 新建t1，t2，t3三个线程，如何保证他们按顺序执行？

### 守护线程

### yield()方法有什么用

### 线程安全与线程不安全

### 怎么检测一个线程是否拥有锁



## GC

什么情况下会出现full gc，什么情况出现young gc

## 字节码

JVM如何加载字节码文件

## JVM内存模型

## Java运行时数据区

## Java内存分配策略

多个线程同时请求内存，如何分配





