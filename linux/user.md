# 用户

1. 创建用户
   
   在root账户下：
   ```
   useradd –d /usr/lhy -m lhy
   ```
   其中-d和-m选项用来为登录名lhy产生一个主目录/usr/lhy。
   
   修改密码：
   ```shell script
   passwd lhy
   ```

2. 赋予sudo权限

    1. 切换到root用户下
    
    2. /etc/sudoers文件默认是只读的
        ```
        chmod u+w /etc/sudoers
        ```
    3. 编辑sudoers文件
    
        找到这行 root ALL=(ALL) ALL
        
        在他下面添加xxx ALL=(ALL) ALL
        
        注：
        ```
        youuser ALL=(ALL) ALL
        %youuser ALL=(ALL) ALL
        youuser ALL=(ALL) NOPASSWD: ALL
        %youuser ALL=(ALL) NOPASSWD: ALL
        ```
        
        第一行:允许用户youuser执行sudo命令(需要输入密码).
        
        第二行:允许用户组youuser里面的用户执行sudo命令(需要输入密码).
        
        第三行:允许用户youuser执行sudo命令,并且在执行的时候不输入密码.
        
        第四行:允许用户组youuser里面的用户执行sudo命令,并且在执行的时候不输入密码.
    
    4. 撤销sudoers文件写权限
        ```
        chmod u-w /etc/sudoers
        ```

