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

timestamp与metric都是array of int of floating point (LZ4压缩)

dimension要稍微复杂一点，由以下三种数据结构构成

1. A dictionary that maps values (which are always treated as strings) to integer IDs,
2. A list of the column’s values, encoded using the dictionary in 1, and
3. For each distinct value in the column, a bitmap that indicates which rows contain that value. (也被称为倒排)

### 示例

![druid-example](https://github.com/chuanlei/tech-notes/blob/master/pics/druid-example.png)


![dimension的格式](https://github.com/chuanlei/tech-notes/blob/master/pics/druid-dimension-data-structure.jpg)

### 倒排

字符串类型的倒排索引

### Segment

