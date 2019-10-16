# 常见FAQ

1. 1 node(s) had taints that the pod didn't tolerate.
    有时候一个pod创建之后一直是pending，没有日志，也没有pull镜像，describe的时候发现里面有一句话： 1 node(s) had taints that the pod didn't tolerate.
    
    直译意思是节点有了污点无法容忍，执行 kubectl get no -o yaml | grep taint -A 5 之后发现该节点是不可调度的。这是因为kubernetes出于安全考虑默认情况下无法在master节点上部署pod，于是用下面方法解决：
    ```
    kubectl taint nodes --all node-role.kubernetes.io/master-
    ```
