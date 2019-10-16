# IDEA 无法添加GO SDK的问题

#### 1.插件源码下载地址
```
https://github.com/go-lang-plugin-org/go-lang-idea-plugin
```
#### 2.源码解压，IDEA打开源码项目
修改gradle.properties文件中，将ideaVersion改为你自己idea版本，修改localIdePath指向你的idea安装目录

#### 3.进入IDEA工程设置
配置sdks，添加jdk和idea platform plugin sdk（idea安装目录）

配置project sdk 为idea platform plugin sdk

配置modules，选中插件工程目录，下一步不要选择创建，选择导入，以gradle方式

配置artifacts，选择jar ---> all modules，ok

#### 4.编译
build artifacts
首次编译会有许多错误，需要将错误都改好了才会成功，可能是因为包版本变化引起的，影响不大
##### 1）id一项删除
##### 2）取变量名字有变化，修改一下就好
##### 3）引用静态变量访问不到，直接改为相应字符串
再次编译，成功
将工程目录的build/idea-sandbox/plugins/Go文件夹拷贝至idea的插件目录，重启idea
