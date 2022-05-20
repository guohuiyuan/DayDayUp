# kubectl

kubectl是k8s的命令行工具
1. 下载kubectl

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

2. 添加执行权限

```
chmod +x kubectl
```

3. 把kubectl添加到环境变量中

```
vi /etc/profile
export PATH=$PATH:/usr/local/kubectl
source /etc/profile
```

4. kubectl 使用 --kubeconfig 指定 config 文件地址访问不同的集群

```
# 获得所有namespaces
kubectl --kubeconfig /usr/local/service/devto/etc/devto.kubeconfig get namespaces

# 获得所有节点
kubectl --kubeconfig /usr/local/service/devto/etc/devto.kubeconfig get nodes --show-labels

# 查看pod运行状态 -A 是查看全部 -o wide 详细列表
kubectl --kubeconfig /usr/local/service/devto/etc/devto.kubeconfig get pod -o wide -A

# node增加标签
kubectl --kubeconfig /usr/local/service/devto/etc/devto.kubeconfig label nodes devops-platform-32-7 nodetag=test1

# node删除标签
kubectl --kubeconfig /usr/local/service/devto/etc/devto.kubeconfig label nodes devops-platform-32-7 nodetag-

```