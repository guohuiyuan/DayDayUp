# 使用kind进行本地k8s部署
- [使用kind进行本地k8s部署](#使用kind进行本地k8s部署)
  - [kind下载](#kind下载)
  - [操作过程](#操作过程)
    - [创建集群](#创建集群)
    - [部署Deployment](#部署deployment)
    - [部署Service](#部署service)
    - [部署nginx-ingress](#部署nginx-ingress)
- [k8s搭建](#k8s搭建)
## kind下载
[快速下载](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)


## 操作过程

### 创建集群

```
kind create cluster --config=k8s/kind/cluster.yaml
```

### 部署Deployment

```
kubectl apply -f k8s/kind/my-dep.yaml

kubectl get pods -o wide
```

### 部署Service

```
kubectl apply -f k8s/kind/my-svc.yaml

kubectl get svc/httpd-svc

kubectl describe svc/httpd-svc

kubectl apply -f k8s/kind/new-my-svc.yaml

kubectl get svc/httpd-svc

kubectl describe svc/httpd-svc
```

### 部署nginx-ingress
[kind安装ingress-nginx](https://kind.sigs.k8s.io/docs/user/ingress/#ingress-nginx)


```
curl -Lo deploy.yaml https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml --ssl-no-revoke

# 参考deploy
https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml

https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

https://raw.githubusercontent.com/Kong/kubernetes-ingress-controller/master/deploy/single/all-in-one-dbless.yaml

```

因为国内无法拉外网的镜像, 所以我们镜像地址替换成阿里云的

```
# 原image
registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.1.1
# 替换镜像
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-webhook-certgen:v1.1.1

# 原image
registry.k8s.io/ingress-nginx/controller:v1.3.0
# 替换镜像
registry.cn-hangzhou.aliyuncs.com/google_containers/nginx-ingress-controller:v1.3.0
```

本地把镜像拉下来, 然后把镜像上传到kind

```
kind load docker-image registry.cn-hangzhou.aliyuncs.com/google_containers/kube-webhook-certgen:v1.1.1

kind load docker-image registry.cn-hangzhou.aliyuncs.com/google_containers/nginx-ingress-controller:v1.3.0

docker exec -it kind-control-plane crictl images
```

安装ingress-nginx

```
# 使用我替换好的配置
kubectl apply -f k8s/kind/deploy.yaml

kubectl get pods --namespace ingress-nginx
```

添加路由和应用

```
kubectl apply -f k8s/kind/usage.yaml
```

测试效果

```
# should output "foo"
curl 你的ip/foo
# should output "bar"
curl 你的ip/bar
```

# k8s搭建
1. kind安装
2. ingress-nginx安装
3. harbor仓库搭建
4. k8s dashboard安装