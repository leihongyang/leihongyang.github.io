# 设置Mysql存储中文

### 方法一
创建数据库的时候要把数据库的碥码也设置好，而之后在创建表，项的时候就不用指定了，因为它们会从上一级继承，
CREATE DATABASE $dbname DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci

### 方法二
修改你安装目录下的my.ini
找到 #default-character-set=utf8
去掉前面的注释符号#，重启mysql服务，这样你现在建立的数据库就可以存储中文了。

