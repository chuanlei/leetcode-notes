# Reactor模型与hadoop-rpc

## 基本版：组件
1. reactor
2. acceptor
3. handler

## 多线程版
1. reactor
2. acceptor
3. reader（线程池）
4. queue
5. handler（线程池）

## 多reactor版
1. main reactor
2. read reactor
3. respond reactor
4. acceptor
5. reader（线程池）
6. queue
7. queue
8. handler（线程池）

## 对应到hadoop rpc
1. `Call`对象
2. `CallQueue`
3. `Handler`

### 执行

![rpc server](https://github.com/chuanlei/tech-notes/blob/master/pics/rpc-server.jpg)

