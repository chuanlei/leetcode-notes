# 线程池原理与实现

## 使用

```java
ExecutorService service = Executors.newCachedThreadPool();
service.execute(new Runnable() {
    @Override
    public void run() {

    }
});

Future<Integer> result = service.submit(new Callable<Integer>() {

    @Override
    public Integer call() throws Exception {
        return 1;
    }
});
```

![executors](https://github.com/chuanlei/tech-notes/blob/master/pics/executors.jpg)

以`newCachedThreadPool()`为例

![newCachedThreadPool](https://github.com/chuanlei/tech-notes/blob/master/pics/newCachedThreadPool.jpg)

所以问题的关键是`ThreadPoolExecutor`

## ThreadPoolExecutor

![thread-pool-executor构造函数](https://github.com/chuanlei/tech-notes/blob/master/pics/thread-pool-executor.jpg)

默认的饱和策略是`AbortPolicy`

基本的任务排队方法有三种
1. 无界队列
2. 有界队列
3. 同步移交

## 扩展ThreadPoolExecutor
1. beforeExecute
2. afterExecute
3. terminated
