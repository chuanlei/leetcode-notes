# 布隆过滤器

## 用例
邮件地址白名单

1G的内存可以提供8\*10^9左右的bit位

指定某哈希函数h：从邮件地址映射到上述bit位中的某一个

我们利用h对上述bit位进行初始化

如果我们的邮件地址是1亿个，那么大约1/8的bit位会被设置为1

应用：过来一个邮件，我们将其映射到bit位，如果发现为1，则通过；否则过滤掉

不幸的是：由于hash冲突问题的存在，某些垃圾邮件会通过

## 布隆过滤器的原理
构成如下
![construction of bloom filter](https://github.com/chuanlei/leetcode-notes/blob/master/pics/construction-of-bf.jpg)

新来的元素经过k次哈希函数的计算，如果有某一位为0，那么必然不能通过。

## 性能分析

### false positive
即不在初始集合，但是“不小心”通过了过滤器。我们要分析的就是false positive的概率。



## 总结
布隆过滤器提供了一种白名单机制，也可以认为是一种注册机制。只有在初识集合中的元素才能通过这个filter
