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

[k8s常用命令](https://www.jianshu.com/p/ea3a83f546ef)

```
# --kubeconfig 指定配置文件
kubectl get namespaces --kubeconfig /usr/local/service/devto/etc/devto.kubeconfig
--kubeconfig /usr/local/service/devto/etc/devto.kubeconfig
--kubeconfig /home/haibaraguo/k8s/devenv.conf

# kubectl在$HOME/.kube目录中查找一个名为config的配置文件
ln -s /home/haibaraguo/k8s/devenv.conf ~/.kube/config

# 获得所有namespaces
kubectl get namespaces

# 获得所有节点
kubectl get nodes --show-labels

# 查看pod运行状态 -A 是查看全部 -o wide 详细列表
kubectl get pod -o wide -A

# 查看pod详细的状态
kubectl describe pod framework-app-qa-69dc6ff85c-hlbhs

# node增加标签
kubectl label nodes devops-platform-32-7 nodetag=test1
kubectl label nodes k8s-node1 devtestops/app=qa 

# node删除标签
kubectl label nodes devops-platform-32-7 nodetag-

# 查看所有的deplyment
kubectl get deployment -A

# 查看deployment详细信息
kubectl describe deployment framework-app-example -n framework

# 进入容器 
kubectl exec pod名称 -it -c 容器名称 /bin/sh
kubectl exec framework-app-qa-75c9bcd555-rdck5 -it -c qa /bin/sh -n framework
kubectl exec -it nginx-ingress-controller-69488949c7-cnt7b -n ingress-nginx -- bash

# 查看配置
kubectl get configmaps -n framework-system

# 查看配置详细状态
kubectl describe configmaps appinfo-for-sms2http-at-team-framework

# 查看ingress
kubectl describe ingress encrypt-cgi2-ingress -n frame

```