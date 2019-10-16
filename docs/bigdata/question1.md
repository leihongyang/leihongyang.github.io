# 搭建相关及各组件用途和关系

1. 主要版本迭代  
    * Hadoop 3.x主要变化(相对于Hadoop 2.x)  
      https://blog.csdn.net/qq_39657909/article/details/84781620
      
    * Spark1.x和Spark2.x的区别  
      * DataFrame与Dataset 统一化了，只剩下DataSet了
      * flatMapToPair 由reture list变为reture  iterator
      * ForeachRDD 不再return null
      * 更新状态的函数中,使用的Optional来自com.google.common.base，函数不能用
      * Spark streaming 中JavaStreamingContextFactory类 废弃
      * Kafka 只提供direct方式 
   
2. 组件分类
    * 数据接入常用的工具有kafka, Flume, Logstash, NDC（网易数据运河系统）, Sqoop
    * 数据预处理: HiveSQL，SparkSQL和Impala等
    * 数据存储: hdfs, hbase, hive, tachyon
    * 数据的可视化以及输出API: 对于处理得到的数据可以对接主流的BI系统，比如国外的Tableau、Qlikview、PowrerBI等，国内的SmallBI和新兴的网易有数等，将结果进行可视化，用于决策分析；或者回流到线上，支持线上业务的发展。

3. 组件介绍
    * Impala能查询存储在Hadoop的HDFS和HBase中的PB级大数据。由于Hive底层执行使用的是MapReduce引擎，仍然是一个批处理过程，难以满足查询的交互性。Hive适合于长时间的批处理查询分析，而Impala适合于实时交互式SQL查询。  
    https://www.cnblogs.com/zlslch/p/6785207.html
    
    * Pig是一个基于Hadoop的大数据分析平台，它提供了一个叫PigLatin的高级语言来表达大数据分析程序，将脚本转换为MapReduce任务在Hadoop上执行。通常用于进行离线分析。

    * *Sqoop数据同步工具，SQL-to-Hadoop的缩写。Sqoop是一个Hadoop和关系型数据库之间的数据转移工具。可将关系型数据库中的数据导入到Hadoop的HDFS中，也可将HDFS中的数据导进到关系型数据库中主要用于传统数据库和Hadoop之前传输数据。数据的导入和导出本质上是Mapreduce程序，充分利用了MR的并行化和容错性。*

    * Flume日志收集工具，Cloudera开源的日志收集系统，具有分布式、高可靠、高容错、易于定制和扩展的特点。它将数据从产生、传输、处理并最终写入目标的路径的过程抽象为数据流，在具体的数据流中，数据源支持在Flume中定制数据发送方，从而支持收集各种不同协议数据。同时，Flume数据流提供对日志数据进行简单处理的能力，如过滤、格式转换等。此外，Flume还具有能够将日志写往各种数据目标（可定制）的能力。总的来说，Flume是一个可扩展、适合复杂环境的海量日志收集系统。

    * *Ambari是一个对Hadoop集群进行监控和管理的基于Web的系统。目前已经支持HDFS，MapReduce，Hive，HCatalog，HBase，ZooKeeper，Oozie，Pig和Sqoop等组件。*



