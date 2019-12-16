# flume

1. 安装
    下载apache-flume-1.9.0-bin.tar.gz并解压
    进入flume目录：
    ```
    cd conf
    cp flume-conf.properties.template netcat.properties
    ``` 
    
    编辑netcat.properties
    ```
   agent.sources = r1
   agent.channels = c1
   agent.sinks = s1
   
   agent.sources.r1.type = netcat
   agent.sources.r1.bind = localhost
   agent.sources.r1.port = 33332
   
   agent.sources.r1.channels = c1
   
   agent.sinks.s1.type = file_roll
   agent.sinks.s1.sink.directory = /tmp/log/test_local
   
   agent.sinks.s1.channel = c1

   agent.channels.c1.type = memory
   
   agent.channels.c1.capacity = 100 
   ```
   
   然后执行
   ```
   bin/flume-ng agent --conf conf -f conf/netcat.properties -n agent &
   ```
   即可启动一个flume agent节点



