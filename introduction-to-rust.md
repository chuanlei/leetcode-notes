# rust入门

## cargo test的用法

## 多线程

最简单的使用方式
```rust
use std::thread;
let child = thread::spawn(move || {
  // 逻辑
});

let res = child.join()
```

工具函数
1. thread::sleep(dur: Duration)
2. thread::yield_now()
3. thread::park() 暂停线程
4. thread::Thread::unparK() 恢复线程

示例
```rust
use std::thread;
use std::time::Duration;

let t = thread::Builder::new()
        .name("child")
        .spawn( move || {
            println!("enter child thread");
            thread::park()
         }).unwrap();
thread::sleep(Duration::new(5,0));
t.thread().unpark();
t.join();
```

### 管道
mpsc（即`std::sync::mpsc`，意为multi-producer, single-consumer FIFO queue）

#### 异步管道
代码示例
```rust
use std::thread;
use std::sync::mpsc::channel;

let (tx, rx) = channel();
thread::spawn(move || {
  for i in 0..10 {
    tx.send(i).unwrap();
  }
});

while let Ok(r) = rx.recv() {
  println!("received {}", r);
}
```

当然，我们可以在tx之上调用`clone()`方法。

### 同步管道
同步管道和异步管道在接收端是一样的逻辑，区别在于发送端。

```rust
use std::thread;
use std::sync::mpsc::sync_channel;

let (tx, rx) = sync_channel();
tx.send(1).unwrap();
thread::spawn(move || {
  tx.send(2).unwrap();
});
println!("received {}", rx.recv().unwrap());
```
