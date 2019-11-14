# CDH

1. 主机运行状态不良
    * 删除agent目录下面的cm_guid文件，并重启失败节点的agent服务恢复。
        * 1) 如果cloudera-manager-agent是离线安装的，那么cm_guid文件的位置是，当初安装cloudera-manager的位置，删除之，然后重启。
                ```
                rm -f /opt/cm-5.11.1/lib/cloudera-scm-agent/cm_guid
                ./cloudera-scm-agent  restart 
                ```
        * 2) 如果cloudera-manager-agent是在线安装的，那么cm-guid文件的位置是/var/lib/cloudera-scm-agent/cm_guid


2. web页面初始化集群失败回滚
    * 进入mysql删除数据库cmdb;
        ```
        drop database cmdb;
        ```
    * 重新执行数据库初始化脚本
        ```
        /opt/cm-5.14.1/share/cmf/schema/scm_prepare_database.sh mysql cmdb -hcentos -uroot --scm-host centos scm scm scm
        ```
      
3. cloudera-scm-server 无故启动不起来，日志查询并无报错信息
    * 检查mysql服务是否启动，mysql未启动的情况下的cloudera-scm-server日志居然没有报错。


