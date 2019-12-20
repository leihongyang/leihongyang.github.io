# spark 部署

1. 前提环境
    1. hadoop已部署
    2. java1.8以上环境
    3. spark 符合hadoop版本的bin包已解压
    
2. 进入spark conf目录
    1. 配置spark-env.sh文件
    ```
    SPARK_LOCAL_IP=master                                 #本机ip或hostname
    SPARK_MASTER_IP=master                                #master节点ip或hostname
    export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop   #hadoop的配置路径
    export YARN_CONF_DIR=/usr/local/hadoop/etc/hadoop     #yarn路径配置
    export JAVA_HOME=/usr/local/jdk
    export HADOOP_HOME=/usr/local/hadoop
    ```
   
   2. 配置slaves文件



