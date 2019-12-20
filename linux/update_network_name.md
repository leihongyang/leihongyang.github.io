# 修改网卡名称

1. 修改/etc/default/grub文件，在GRUB_CMDLINE_LINUX一行中添加net.ifnames=0 biosdevname=0
   ```
   GRUB_TIMEOUT=5
   GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
   GRUB_DEFAULT=saved
   GRUB_DISABLE_SUBMENU=true
   GRUB_TERMINAL_OUTPUT="console"
   GRUB_CMDLINE_LINUX="crashkernel=auto rhgb quiet net.ifnames=0 biosdevname=0"
   GRUB_DISABLE_RECOVERY="true"
   ```
   
   然后运行
   ```
   grub2-mkconfig -o /boot/grub2/grub.cfg
   ```
   重启reboot

2. 修改/etc/udev/rules.d/70-persistent-net.rules文件,添加一行

   ```
   SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="80:61:5f:01:c1:d0", ATTR{type}=="1", KERNEL=="eth*", NAME="em1"
   ```
   
   其中ATTR{address}的值为要修改的网卡的MAC地址，通过ip a可以看到
   
   KERNEL不需要做修改

3. 修改网卡配置文件，配置DEVICE和NAME，并加入HWADDR

   注：DEVICE、NAME和HWADDR 要和.rules 里面相对应
   ```
   NAME=em1
   HWADDR=80:61:5f:01:c1:d0
   DEVICE=em1
   ```
   ```
   TYPE=Ethernet
   PROXY_METHOD=none
   BROWSER_ONLY=no
   BOOTPROTO=none
   DEFROUTE=yes
   IPV4_FAILURE_FATAL=no
   IPV6INIT=yes
   IPV6_AUTOCONF=yes
   IPV6_DEFROUTE=yes
   IPV6_FAILURE_FATAL=no
   IPV6_ADDR_GEN_MODE=stable-privacy
   UUID=878eecf0-0d86-4f0a-911b-b6ec9004fd3b
   ONBOOT=yes
   IPADDR=192.168.0.245
   PREFIX=24
   GATEWAY=192.168.0.1
   DNS1=114.114.114.114
   IPV6_PRIVACY=no
   ```

