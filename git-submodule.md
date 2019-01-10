# git子模块

[参考资料](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)

使用效果如下

```shell
git clone --recursive --branch stable https://github.com/yandex/ClickHouse.git
```

这是一种源码共享方案

## 添加一个远程仓库

```shell
git submodule add https://github.com/chaconinc/DbConnector
```

多了一个`.gitmodules`文件。

## 克隆含有子模块的项目

当你在克隆这样的项目时，默认会包含该子模块目录，但其中还没有任何文件。

 你必须运行两个命令：`git submodule init` 用来初始化本地配置文件，而 `git submodule update` 则从该项目中抓取所有数据并检出父项目中列出的合适的提交。
 
 不过还有更简单一点的方式。 如果给 git clone 命令传递 --recursive 选项，它就会自动初始化并更新仓库中的每一个子模块
 
 ```shell
 git clone --recursive https://github.com/chaconinc/MainProject
 ```
 
 
