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
