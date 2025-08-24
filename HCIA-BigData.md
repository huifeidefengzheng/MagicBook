# HCIA-BigData

## 一、大数据的任务类型

1. IO密集型任务（IO-Intensive）
   - 涉及到网络、磁盘、内存IO的任务都是IO密集型任务
   - 特点：CPU消耗少，任务的大部分时间都在等待IO操作完成，（因为IO的速度远远低于CPU和内存的速度）
   - IO密集型任务多，CPU效率高，99%的时间都花在IO上，花在CPU上的时间少
2. 计算密集型任务
   - 特点：要进行大量的计算，消耗CPU资源，比较计算圆周率、对视频进行高清解码等，全款CPU的运算能力；
   - 计算密集型任务虽然也可以用多任务完成，但是任务多，花在任务切换的时间就越多，cpu执行任务效率就低，
   - 由于主要消耗CPU资源，因此代码运行效率高至关重要。
3. 数据密集型任务
   - 大量独立的数据分析处理作业可以分布在松耦合的计算机集群系统的不同节点上运行
   - 高度密集的海量数据I/O吞吐需求
   - 大部分数据密集型应用有个数据驱动的流程
   - 分类：
     - 日志分析
     - 软件即服务Saas应用
     - 大型企业的商务智能应用

## 二、大数据应用的主要计算模式

1. 批处理计算
   - 针对大规模数据的批量处理，主要技术MapReduce、Spark等
2. 流计算
   - 针对流数据的实时技术处理，主要技术：Spark、Storm、Flink、Dstream等
3. 图计算
   - 针对大规模图结果数据的处理，主要技术：GraphX、Gelly、Giraph、PowerGraph
4. 查询分析计算
   - 大规模数据的存储管理和查询分析，主要技术：Hive、Impala、Dremel、Cassandra等

![image-20230416225350587](E:\magic-book\HCIA-BigData.assets\image-20230416225350587.png)

![image-20230416231347869](E:\magic-book\HCIA-BigData.assets\image-20230416231347869.png)

![image-20230416231414376](E:\magic-book\HCIA-BigData.assets\image-20230416231414376.png)

![image-20230416231503761](E:\magic-book\HCIA-BigData.assets\image-20230416231503761.png)

![image-20230416231547015](E:\magic-book\HCIA-BigData.assets\image-20230416231547015.png)

![image-20230416231635320](E:\magic-book\HCIA-BigData.assets\image-20230416231635320.png)

![image-20230416231713448](E:\magic-book\HCIA-BigData.assets\image-20230416231713448.png)

![image-20230416231840337](E:\magic-book\HCIA-BigData.assets\image-20230416231840337.png)

![image-20230416231922552](E:\magic-book\HCIA-BigData.assets\image-20230416231922552.png)

