# druid

## 架构
![druid-architecture](https://github.com/chuanlei/tech-notes/blob/master/pics/druid-architecture.png)

1. coordinator
2. overlord
3. broker
4. historical
5. middleManager
6. peon

### 外部依赖
1. zk (view of the cluster: data topology)
2. mysql
3. hdfs

### 查询类型
1. TimeSeries
2. TopN
3. GroupBy
4. Time Boundary
5. Search
6. Select

### 查询的构成
1. query type
2. granularity
3. filter
4. aggregation
5. post-aggregation


## 数据格式

### 倒排

字符串类型的倒排索引

### Segment

