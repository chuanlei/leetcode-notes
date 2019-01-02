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

![线程状态机](https://github.com/chuanlei/tech-notes/blob/master/pics/FTHOR.png)

BLOCKED与WAITING有什么区别

### volatile关键字

![tso模型](https://github.com/chuanlei/tech-notes/blob/master/pics/tso%E6%A8%A1%E5%9E%8B.jpg)

`volatile`关键字的用意是将被该关键字修饰的成员变量及时被刷新到主内存里面。

但是`volatile`并不能保证线程安全性。它除了可见性之外还有防止指令重排优化的功能。

#### 指令重排的应用:双重懒加载的单例模式

```java
public class Singleton {

    private static volatile Singleton singleton;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    //防止指令重排
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```



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

### 标记-清除算法

### 复制算法

### 标记整理算法

### 分代回收算法

## 字节码

JVM如何加载字节码文件

## JVM内存模型

## Java运行时数据区

## 对象的创建与内存分配

通常由两种方式
1. 指针碰撞
2. 空闲列表

可以将内存分配安排在每个线程独有的空间进行，每个线程首先在堆内存中分配一小块内存，称为本地分配缓存(TLAB : Thread Local Allocation Buffer)。

分配内存时，只需要在自己的分配缓存中分配即可，由于这个内存区域是线程私有的，所以不会出现并发问题。

可以使用 -XX:+/-UseTLAB 参数来设定 JVM 是否开启 TLAB

内存分配之后需要对该对象进行设置，如[对象头](https://github.com/crossoverJie/JCSprout/blob/master/MD/Synchronize.md)

多个线程同时请求内存，如何分配







