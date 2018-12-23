# The Log-Structured Merge-Tree

## 参考资料
[The Log-Structured Merge-Tree](https://blog.acolyer.org/2014/11/26/the-log-structured-merge-tree-lsm-tree/)

## 背景

我们介绍两种存储引擎家族：日志结构的存储引擎和面向页的存储引擎(比如B-tree)

## 索引
适当的索引可以加速读取查询，但每个索引都会减慢写速度。

### 哈希索引
在实践中真正需要考虑的问题
1. 文件格式
2. 删除记录（墓碑）
3. 崩溃恢复（内存hash表的恢复）
4. 部分写入的记录
5. 并发控制

上面这些结构有着很多的弊端，比如
1. 哈希表必须全部放入内存
2. 区间查询效率不高

### SSTables和LSM-Tree
要求key-value对的顺序按键排序，这种格式称为排序字符串表，或简称为SSTable

这种格式的调整带来以下好处
1. 合并段更加简单高效
2. 在文件中查找特定的键时，不再需要在内存中保存所有键的索引

#### 构建和维护SSTable
SSTable有序的特性与顺序写入的要求不符合。

方案：先写内存（内存可以是红黑树或者AVL树之类的）

#### 从SSTable到LSM-Tree

#### 性能优化
1. 用bloom filter避免在不存在的键上面浪费太多时间。
2. 分层压缩（参考资料：Choosing Between Efficiency and Performance with RocksDB)

## B-Trees
B-Tree将数据库分解成固定大小的块或页，传统上大小为4kB，页是内部读/写的最小单元。

页面引用构成一个树状页面。

### B-Tree的优化
1. 写时复制
2. 保存键的缩略信息，而不是完整的键
3. 在同一层中添加额外的指针

