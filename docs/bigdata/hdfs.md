# hdfs

1. 架构原理
    * 一次写入，多次读取
    * 大文件块存储

2. hdfs容错机制
    * hdfs replication副本数，多副本数保证第一重容错
    * Hadoop的机架感知，HDFS和Map/Reduce的组件是能够感知机架的：
    
      NameNode和JobTracker通过调用管理员配置模块中的APIresolve来获取集群里每个slave的机架id。
      该API将slave的DNS名称（或者IP地址）转换成机架id。
      使用哪个模块是通过配置项topology.node.switch.mapping.impl来指定的。
      模块的默认实现会调用topology.script.file.name配置项指定的一个的脚本/命令。 
      如果topology.script.file.name未被设置，对于所有传入的IP地址，模块会返回/default-rack作为机架id。
      在Map/Reduce部分还有一个额外的配置项mapred.cache.task.levels，该参数决定cache的级数（在网络拓扑中）。
      例如，如果默认值是2，会建立两级的cache－ 一级针对主机（主机 -> 任务的映射）另一级针对机架（机架 -> 任务的映射）。

3. 安全模式
    * Namenode启动后会进入一个称为安全模式的特殊状态。
    处于安全模式的Namenode是不会进行数据块的复制的。
    
    * Namenode从所有的 Datanode接收心跳信号和块状态报告。
    块状态报告包括了某个Datanode所有的数据块列表。每个数据块都有一个指定的最小副本数。
    
    * 当Namenode检测确认某个数据块的副本数目达到这个最小值，那么该数据块就会被认为是副本安全(safely replicated)的；
    在一定百分比（这个参数可配置）的数据块被Namenode检测确认是安全之后（加上一个额外的30秒等待时间），
    Namenode将退出安全模式状态。接下来它会确定还有哪些数据块的副本没有达到指定数目，并将这些数据块复制到其他Datanode上。

4. DFSShell
    * bin/hadoop dfs -mkdir /foodir 创建文件夹
    * bin/hadoop dfs -rm -r /foodir 删除文件夹
    
    所有命令类似linux命令
    
5. DFSAdmin
    * 将集群置于安全模式	```bin/hadoop dfsadmin -safemode enter```
    * 显示Datanode列表	```bin/hadoop dfsadmin -report```
    * 使Datanode节点 datanodename退役	```bin/hadoop dfsadmin -decommission datanodename```


