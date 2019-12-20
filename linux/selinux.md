# 查看 SELinux状态及关闭SELinux
1. 查看 SELinux状态
    1. 查看SELinux状态：
    
        如果SELinux status参数为enabled即为开启状态
        ```
        /usr/sbin/sestatus -v      
        ```
        SELinux status:                 enabled
    
    2. getenforce 

2. 关闭SELinux：

    1. 临时关闭（不用重启机器）：
        ```
        setenforce 0      ##设置SELinux 成为permissive模式
                          ##setenforce 1 设置SELinux 成为enforcing模式
        ```
    2. 修改配置文件需要重启机器：
    
        修改/etc/selinux/config 文件
        
        将SELINUX=enforcing改为SELINUX=disabled
        
        重启机器即可

3. 防火墙
    1. 查看状态
        ```
        firewall-cmd --state
        ```
    2. 停止firewall
        ```
        systemctl stop firewalld.service
        ```
    3. 禁止firewall开机启动
        ```
        systemctl disable firewalld.service
        ```
