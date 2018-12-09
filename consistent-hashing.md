# 一致性哈希

## 参考资料
[Consistent Hashing: Algorithmic Tradeoffs](https://medium.com/@dgryski/consistent-hashing-algorithmic-tradeoffs-ef6b8e2fcae8)

## 优秀的函数特性
![optimal functions](https://github.com/chuanlei/leetcode-notes/blob/master/pics/optimal-functions.jpg)

该方案在1997年CDN相关论文中提出，在2007年memcache和dynamo中发扬光大。

我们可以想象一个0..2^32-1的整数构成的环(ring)。

基本想法是将server映射到环上的某个点；如果需要根据key查找对应的server，那么从映射到的点开始顺序扫描。
