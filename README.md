# RTFM
[RTFM](https://zh.wikipedia.org/zh-cn/RTFM)，是一个英文缩写，意思是：“去读TMD手册”（Read The F**king Manual），这句话通常用在回复那些只要查阅文件就可以解决，拿出来提问只是浪费别人时间的问题。



这个项目是一个私人项目，用来整理那些我在日常工作学习中，读过的手册。



- Cloudera：由Cloudera公司在其官方网站公布的一些手册文档。
  - [Cloudera裸机部署参考架构.md](cloudera/Cloudera裸机部署参考架构.md) ：这个手册讲了一些如何为Hadoop集群选择硬件、部署网络、调优操作系统参数。比如选择HDD还是SSD、磁盘是大一些还是小一些，HDFS使用EC替代3副本策略时如何保证可用性等等。
  - [集群主机和角色的分配建议](cloudera/集群主机和角色的分配建议.md) : 这个文档是CDH安装文档的一部分，主要是不同规模的集群，应当如何划分不同的主机并分配适当的角色。比如：主节点部署什么角色、工作节点部署什么角色、工具节点部署什么角色、网关节点部署什么角色。

- Scala：和Scala相关的一些文档。
  - [Scala使用Maven](scala/scala-with-maven-zh_CN.md)：来自Scala的官方手册，不过好像被隐藏掉了。

- Management：和管理相关的书籍和手册。
  - [OKR介绍.md](management/OKR介绍.md) :这个是Christina Wodtke写的介绍OKR的。有关于什么是OKR，以及如何在组织中开展OKR。