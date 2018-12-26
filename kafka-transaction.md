# Kafka事务机制

## 参考资料

[Exactly-once Semantics are Possible: Here’s How Kafka Does it](https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/)

[Transactions in Apache Kafka](https://www.confluent.io/blog/transactions-apache-kafka/)

[KIP-98 - Exactly Once Delivery and Transactional Messaging](https://cwiki.apache.org/confluence/display/KAFKA/KIP-98+-+Exactly+Once+Delivery+and+Transactional+Messaging)

## 基本使用

引入依赖

```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>2.0.0</version>
</dependency>
```

### 生产者

```java
Properties producerProps = new Properties();
producerProps.put("bootstrap.servers", "localhost:9092");
```

对生产者进行配置

```java
producerProps.put("enable.idempotence", "true");
producerProps.put("transactional.id", "prod-1");
 
KafkaProducer<String, String> producer = new KafkaProducer(producerProps);
```

如此可以保证生产端的幂等性

进入事务感知模式

```java
producer.initTransactions();
```

### 消费者

```java
Properties consumerProps = new Properties();
consumerProps.put("bootstrap.servers", "localhost:9092");
consumerProps.put("group.id", "my-group-id");
consumerProps.put("enable.auto.commit", "false");
consumerProps.put("isolation.level", "read_committed");
KafkaConsumer<String, String> consumer = new KafkaConsumer<>(consumerProps);
consumer.subscribe(singleton(“sentences”));
```

**消费数据**

```java
ConsumerRecords<String, String> records = consumer.poll(ofSeconds(60));
Map<String, Integer> wordCountMap =
  records.records(new TopicPartition("input", 0))
    .stream()
    .flatMap(record -> Stream.of(record.value().split(" ")))
    .map(word -> Tuple.of(word, 1))
    .collect(Collectors.toMap(tuple -> 
      tuple.getKey(), t1 -> t1.getValue(), (v1, v2) -> v1 + v2));
```


**发送数据**

```java
producer.beginTransaction();
wordCountMap.forEach((key,value) -> 
    producer.send(new ProducerRecord<String,String>("counts",key,value.toString())));
```

## 原理

### 幂等性发送

需要解决的问题

1. Broker保存消息后，发送ACK前宕机，Producer认为消息未发送成功并重试，造成数据重复
2. 前一条消息发送失败，后一条消息发送成功，前一条消息重试后成功，造成数据乱序



