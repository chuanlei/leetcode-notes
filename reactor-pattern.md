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
 
`Acceptor`的`run()`方法

```scala
  /**
   * Accept loop that checks for new connection attempts
   */
  def run() {
    serverChannel.register(nioSelector, SelectionKey.OP_ACCEPT)
    var currentProcessor = 0
    while (isRunning) {
      val ready = nioSelector.select(500)
      if (ready > 0) {
        val keys = nioSelector.selectedKeys()
        val iter = keys.iterator()
        while (iter.hasNext && isRunning) {
            val key = iter.next
            iter.remove()
            if (key.isAcceptable) {
              val processor = synchronized {
                currentProcessor = currentProcessor % processors.size
                processors(currentProcessor)
              }
              accept(key, processor)
            }
            // round robin to the next processor thread, mod(numProcessors) will be done later
            currentProcessor = currentProcessor + 1
        }
      }
    }
  }
```

其中`accept()`方法的实现为

```scala
    /*
   * Accept a new connection
   */
  def accept(key: SelectionKey, processor: Processor) {
    val serverSocketChannel = key.channel().asInstanceOf[ServerSocketChannel]
    val socketChannel = serverSocketChannel.accept()
    socketChannel.configureBlocking(false)
    socketChannel.socket().setTcpNoDelay(true)
    socketChannel.socket().setKeepAlive(true)
    if (sendBufferSize != Selectable.USE_DEFAULT_BUFFER_SIZE)
      socketChannel.socket().setSendBufferSize(sendBufferSize)

    processor.accept(socketChannel)  // 加入processor的newConnections对象，同时唤醒processor中的selector对象
  }
```

`Processor` 的成员变量为

```scala
val newConnections = new ConcurrentLinkedQueue[SocketChannel]()
val inflightResponses = mutable.Map[String, RequestChannel.Response]()
val responseQueue = new LinkedBlockingDeque[RequestChannel.Response]()
```

**`Processor`中的循环为**

```scala
  override def run() {
    while (isRunning) {
      // setup any new connections that have been queued up
      configureNewConnections()
      // register any new responses for writing
      processNewResponses()
      poll()
      processCompletedReceives()
      processCompletedSends()
      processDisconnected()
    }
  }
```

其中`configureNewConnections()`的逻辑为：从`newConnections`中取出`socketChannel`，进而

```scala
  protected[network] def connectionId(socket: Socket): String = {
    val localHost = socket.getLocalAddress.getHostAddress
    val localPort = socket.getLocalPort
    val remoteHost = socket.getInetAddress.getHostAddress
    val remotePort = socket.getPort
    val connId = ConnectionId(localHost, localPort, remoteHost, remotePort, nextConnectionIndex).toString
    nextConnectionIndex = if (nextConnectionIndex == Int.MaxValue) 0 else nextConnectionIndex + 1
    connId
  }
  
  SelectionKey key = socketChannel.register(nioSelector, SelectionKey.OP_READ);
  KafkaChannel channel = channelBuilder.buildChannel(id, key, maxReceiveSize, memoryPool);
  key.attach(channel);
```


## 对应到hadoop rpc
1. `Call`对象
2. `CallQueue`
3. `Handler`

### 执行

![rpc server](https://github.com/chuanlei/tech-notes/blob/master/pics/rpc-server.jpg)

