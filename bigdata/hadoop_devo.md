# hadoop 开发中的坑

1. java.io.FileNotFoundException: HADOOP_HOME and hadoop.home.dir are unset
    * 多发生于windows环境下远程开发hadoop程序
    * 解决方案：
        * 下载hadoop-bin包，解压到windows某路径
        * 下载https://github.com/leihongyang/winutils/tree/master/hadoop-3.2.1/bin下所有文件到windows上hadoop的bin目录
        * 设置HADOOP_HOME环境变量及bin目录到PATH变量

