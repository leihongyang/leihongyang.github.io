# hadoop 搭建FAQ

1. datanode 日志出现错误信息
    ```
    2019-08-12 09:16:10,261 WARN org.apache.hadoop.hdfs.server.datanode.DataNode: Problem connecting to server: kycluster:9000
    ```
    将etc/hadoop/core-site.xml中的fs.defaultFS改为IP
    
2. 防火墙问题
   ```
   执行一下命令：
   
   systemctl stop firewalld
   systemctl mask firewalld
   
   并且安装iptables-services：
   yum install iptables-services
   
   设置开机启动：
   systemctl enable iptables
   
   systemctl stop iptables
   systemctl start iptables
   systemctl restart iptables
   systemctl reload iptables
   
   保存设置：
   service iptables save
  
   OK，再试一下应该就好使了
 
   开放某个端口 在/etc/sysconfig/iptables里添加
   -A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
   ```
   
3. 在集群正常的情况下，Hadoop 50070 无法访问
    * 查看50070端口是否打开，若已打开，检查防火墙
    * 若未打开，检查etc/hadoop/hdfs-site.xml中是否配置dfs.http.address
        ```
        <property>
          <name>dfs.http.address</name>
          <value>0.0.0.0:50070</value>
        </property>
        ```
        关闭集群，重启集群

4. spark程序启动报错

    在hive/conf/hive-site.xml中
     ```
     <!—该配置是关闭hive元数据版本认证，否则会在启动spark程序时报错-->
     <property>
       <name>hive.metastore.schema.verification</name>
       <value>false</value>
     </property>
     ```
