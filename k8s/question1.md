# k8s单点部署

参考文档：https://www.kubernetes.org.cn/5650.html

1. 公共部分(在master和worker都要运行)
    * 卸载旧版本
    ```
   sudo yum remove -y docker \
   docker-client \
   docker-client-latest \
   docker-common \
   docker-latest \
   docker-latest-logrotate \
   docker-logrotate \
   docker-selinux \
   docker-engine-selinux \
   docker-engine
   ```
   
   * 设置 yum repository
   
   ```
   sudo yum install -y yum-utils \
   device-mapper-persistent-data \
   lvm2
   
   sudo yum-config-manager \
   --add-repo \
   https://download.docker.com/linux/centos/docker-ce.repo
   ```
   
   * 安装并启动 docker
   
   ```
   sudo yum install -y docker-ce-18.09.7 docker-ce-cli-18.09.7 containerd.io
   sudo systemctl enable docker
   sudo systemctl start docker
   ```
   
   * 安装 nfs-utils
   
   ```
   sudo yum install -y nfs-utils
   ```
   
   * 配置K8S的yum源
   
   ```
   cat <<EOF > /etc/yum.repos.d/kubernetes.repo
   [kubernetes]
   name=Kubernetes
   baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
   enabled=1
   gpgcheck=0
   repo_gpgcheck=0
   gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
          http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
   EOF
   ```
   
   * 关闭 防火墙、SeLinux、swap
   
   ```
   systemctl stop firewalld
   systemctl disable firewalld
   
   setenforce 0
   sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
   
   swapoff -a
   yes | cp /etc/fstab /etc/fstab_bak
   cat /etc/fstab_bak |grep -v swap > /etc/fstab
   ```
   
   * 修改 /etc/sysctl.conf
   
   ```
   vim /etc/sysctl.conf
   ```
   如下
   
   ```
   net.ipv4.ip_forward = 1
   net.bridge.bridge-nf-call-ip6tables = 1
   net.bridge.bridge-nf-call-iptables = 1
   ```
   然后执行命令应用
   
   ```
   sysctl -p
   ```
   
   * 安装kubelet、kubeadm、kubectl
   
   ```
   yum install -y kubelet-1.15.1 kubeadm-1.15.1 kubectl-1.15.1
   ```
   
   ```
   vim /usr/lib/systemd/system/docker.service
   ```
   在ExecStart值最后加上
   ```
   --exec-opt native.cgroupdriver=systemd
   ```
   
   * 设置 docker 镜像
   
   ```
   curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s https://registry.docker-cn.com
   ```
   
   * 重启 docker，并启动 kubelet
   
   ```
   systemctl daemon-reload
   systemctl restart docker
   systemctl enable kubelet && systemctl start kubelet
   ```
   
2. 初始化 master 节点(以下只在master节点运行)
    * 配置 apiserver.demo 的域名
    
    ```
   echo "x.x.x.x  apiserver.demo" >> /etc/hosts
   ```
   
   * 创建 ./kubeadm-config.yaml
   
   ```
   cat <<EOF > ./kubeadm-config.yaml
   apiVersion: kubeadm.k8s.io/v1beta1
   kind: ClusterConfiguration
   kubernetesVersion: v1.15.1
   imageRepository: registry.cn-hangzhou.aliyuncs.com/google_containers
   controlPlaneEndpoint: "apiserver.demo:6443"
   networking:
     podSubnet: "10.100.0.1/20"
   EOF
   ```
   
   * 初始化 apiserver
   
   ```
   kubeadm init --config=kubeadm-config.yaml --upload-certs
   ```
   
   * 初始化 root 用户的 kubectl 配置
   
   ```
   rm -rf /root/.kube/
   mkdir /root/.kube/
   cp -i /etc/kubernetes/admin.conf /root/.kube/config
   ```
   
   * 安装 calico
   
   ```
   kubectl apply -f https://docs.projectcalico.org/v3.6/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
   ```
   等待calico安装就绪：
   
   执行如下命令，等待 3-10 分钟，直到所有的容器组处于 Running 状态
   
   ```
   watch kubectl get pod -n kube-system
   ```
   
   检查master结果
   
   ```
   kubectl get nodes
   ```
   
   



