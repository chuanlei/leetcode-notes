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
7. handler（线程池）

## Kafka Socket Server

**SocketServer**类

```
* An NIO socket server. The threading model is
 *   1 Acceptor thread that handles new connections
 *   Acceptor has N Processor threads that each have their own selector and read requests from sockets
 *   M Handler threads that handle requests and produce responses back to the processor threads for writing.
 */
 ```
 
 **`Acceptor`的启动过程**
 
 ```scala
 private val nioSelector = NSelector.open()
 val serverChannel = openServerSocket(endPoint.host, endPoint.port)
 private val processors = new ArrayBuffer[Processor]()
 ```
 
 **`openServerSocket`的逻辑为**
 
 ```scala
   /*
   * Create a server socket to listen for connections on.
   */
 private def openServerSocket(host: String, port: Int): ServerSocketChannel = {
    val socketAddress = new InetSocketAddress(port)
    val serverChannel = ServerSocketChannel.open()
    serverChannel.configureBlocking(false)
    if (recvBufferSize != Selectable.USE_DEFAULT_BUFFER_SIZE)
      serverChannel.socket().setReceiveBufferSize(recvBufferSize)
    serverChannel.socket.bind(socketAddress)
 }
```

往其中添加`processor`的流程为

```scala
  private def addProcessors(acceptor: Acceptor, endpoint: EndPoint, newProcessorsPerListener: Int): Unit = synchronized {
    val listenerName = endpoint.listenerName
    val securityProtocol = endpoint.securityProtocol
    val listenerProcessors = new ArrayBuffer[Processor]()

    for (_ <- 0 until newProcessorsPerListener) {
      val processor = newProcessor(nextProcessorId, connectionQuotas, listenerName, securityProtocol, memoryPool)
      listenerProcessors += processor
      requestChannel.addProcessor(processor)
      nextProcessorId += 1
    }
    listenerProcessors.foreach(p => processors.put(p.id, p))
    acceptor.addProcessors(listenerProcessors)
  }
```
 

## 对应到hadoop rpc
1. `Call`对象
2. `CallQueue`
3. `Handler`

### 执行

![rpc server](https://github.com/chuanlei/tech-notes/blob/master/pics/rpc-server.jpg)

