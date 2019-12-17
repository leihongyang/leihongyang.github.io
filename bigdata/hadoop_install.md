# hadoop 完全分布式部署安装

1. 环境准备
    * centos或者ubuntu等任意linux系统（或带有Cygwin的windows系统）
    * jdk环境（hadoop 3.x需要jdk8以上版本）
    * 网络可用，至少节点间互通，最好设置为静态ip
    * hosts文件中将实际ip和hostname映射起来，所有节点都写进去
    * 多个节点ssh免密

2. 解压文件并配置环境变量
    * 将hadoop-bin.tar.gz解压至要安装的目录
    * 编辑/etc/profile
      ```
      # 添加hadoop环境变量
      export HADOOP_HOME=/usr/local/hadoop
      export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
      
      # 添加zk环境变量（可选）
      export ZOOKEEPER_HOME=/usr/local/zookeeper
      export PATH=$PATH:$ZOOKEEPER_HOME/bin
      ```
    * ```source /etc/profile```

3. zk安装（可选）
    * 编辑配置文件
    ```
        cp /usr/local/zookeeper/conf/zoo_sample.cfg /usr/local/zookeeper/conf/zoo.cfg
            tickTime=2000
            dataDir=/opt/data/zookeeper # 数据存放路径
            clientPort=2181
            initLimit=5
            syncLimit=2
            server.1=node2:2888:3888
            server.2=node3:2888:3888
            server.3=node4:2888:3888
    ```
   * 将配置文件复制到其他节点
        ```
        scp /usr/local/zookeeper/conf/zoo.cfg node2:/usr/local/zookeeper/conf/
        ```
   * 创建节点ID，在配置的 dataDir 路径中添加myid文件
       ```
       echo "1" > myid
       ```
   * 启动 zookeeper
       ```
       zkServer.sh start
       ```
   * 检验
       ```
       jps
       ```
   * 配置zk开机自启（可选）
       * 参考：https://blog.csdn.net/afgasdg/article/details/79277926

4. hadoop配置文件
    * http://hadoop.apache.org/docs/r3.0.0/hadoop-project-dist/hadoop-hdfs/hdfs-default.xml
    * hdfs-site.xml
        * ```
          <configuration>
            <!-- 这里仅当需要配置多个namenode时配置
              <property>
                  <!--这里配置逻辑名称 -->
                  <name>dfs.nameservices</name>
                  <value>hbzx</value>
              </property>
              <property>
                  <!-- 配置namenode 的名称，多个用逗号分割  -->
                  <name>dfs.ha.namenodes.hbzx</name>
                  <value>nn1,nn2</value>
              </property>
            -->
            <property>
                <name>dfs.permissions</name>
                <value>false</value>
            </property>
            <property>
                <name>dfs.replication</name>
                <value>2</value>
            </property>
            <property>
                <name>dfs.http.address</name>
                <value>0.0.0.0:50070</value>
            </property>
          <!--指定hdfs中namenode的存储位置-->
          <property>
               <name>dfs.namenode.name.dir</name> 
               <value>file:/usr/hadoop/dfs/name</value>
          </property>
          <!--指定hdfs中datanode的存储位置-->
          <property>
               <name>dfs.datanode.data.dir</name>
               <value>file:/usr/hadoop/dfs/data</value>
          </property>
          </configuration>
          ```
    * core-site.xml
        * ```
          <configuration>
            <property>
                <name>hadoop.tmp.dir</name>
                <value>/tmp/hadoop/data/tmp</value>
            </property>
            <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:9000</value>
            </property>
            <!-- 当需要指定zk的时候
                <property>
                    <!-- 指定zookeeper所在的节点 -->
                    <name>ha.zookeeper.quorum</name>
                    <value>node2:2181,node3:2181,node4:2181</value>
                </property>
            -->
          </configuration>
          ```
    * yarn-site.xml
        * ```
          <configuration>
            <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
            </property>
            <property>
                <name>yarn.nodemanager.vmem-check-enabled</name>
                <value>false</value>
            </property>
          </configuration>
          ```
    * mapred-site.xml
        * ```
          <configuration>
            <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
            </property>
            <!-- 可选
                <property>
                    <name>yarn.app.mapreduce.am.env</name>
                    <value>HADOOP_MAPRED_HOME=${HADDOP_HOME}</value>
                </property>
                <property>
                    <name>mapreduce.map.env</name>
                    <value>HADOOP_MAPRED_HOME=${HADDOP_HOME}</value>
                </property>
                <property>
                    <name>mapreduce.reduce.env</name>
                    <value>HADOOP_MAPRED_HOME=${HADDOP_HOME}</value>
                </property>
            -->
          </configuration>
          ```
    * hadoop-env.sh
        * ```
          export JAVA_HOME=${JAVA_HOME}
          export HADOOP_HOME=${HADDOP_HOME}
          ```
    * workers
        * ```
          node2
          node3
          node4
          ```
4. 将配置文件复制到其他机器
    * scp hadoop/etc/hadoop/* node-xxx:/usr/local/hadoop/etc/hadoop/

5. 启动
    * 先启动zookeeper（可选）
        * ```zkServer.sh start```
    * 在其中一个namenode上格式化zookeeper（可选）
        * ```hdfs zkfc -formatZK```
    * 启动journalnode,需要启动所有节点的journalnode（可选）
        * ```hdfs --daemon start journalnode```
    * 格式化namenode
        ```
        hdfs namenode -format 
        # 如果有多个namenode名称，可以使用  hdfs namenode -format xxx 指定
        ```
    * 启动namenode（可选）
        ```
        hdfs --daemon start namenode
        ```
    * 其他namenode同步（可选）
        * 如果是使用高可用方式配置的namenode，使用下面命令同步(需要同步的namenode执行).
        ```
        hdfs namenode -bootstrapStandby
        ```
        * 如果不是使用高可用方式配置的namenode，使用下面命令同步
        ```
        hdfs namenode -initializeSharedEdits
        ```
    * 启动hdfs
      ```
      sbin/start-dfs.sh
      ```

5. FAQ
    * 使用root配置的hadoop并启动会出现报错
        错误：
        ```
                Starting namenodes on [master]
                ERROR: Attempting to operate on hdfs namenode as root
               
                ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
               
                Starting datanodes
                ERROR: Attempting to operate on hdfs datanode as root
               
                ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
                Starting secondary namenodes [slave1]
                ERROR: Attempting to operate on hdfs secondarynamenode as root
                ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.
        ```
        解决方法：
        ```
                 在/hadoop/sbin路径下：
                 将start-dfs.sh，stop-dfs.sh两个文件顶部添加以下参数
                      HDFS_DATANODE_USER=root
                      HADOOP_SECURE_DN_USER=hdfs
                      HDFS_NAMENODE_USER=root
                      HDFS_SECONDARYNAMENODE_USER=root
                 start-yarn.sh，stop-yarn.sh顶部也需添加以下
                    YARN_RESOURCEMANAGER_USER=root
                    HADOOP_SECURE_DN_USER=yarn
                    YARN_NODEMANAGER_USER=root
        ```
