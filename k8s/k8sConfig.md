# k8s常用的配置文件
<!-- GFM-TOC -->
* [k8s常用的配置文件](#k8s常用的配置文件)
    * [namespace配置文件](#namespace配置文件)
    * [pod配置文件](#pod配置文件)
    * [label配置文件](#label配置文件)
    * [depolyment配置文件](#deployment配置文件)
    * [service配置文件](#service配置文件)
<!-- GFM-TOC -->

## namespace配置文件

```
apiVersion: v1
kind: Namespace
metadata: 
 name: dev
```

然后执行命令创建
kubectl create -f namespace-dev.yaml

## pod配置文件

```
apiVersion: v1
kind: Pod
metadata: 
  name: pod-name
  namespace: dev
spec: 
  containers: 
    - image: nginx:1.17.1
      name: nginx-container
      ports: 
        - name: nginx-port
          containerPort: 80
          protocol: TCP
```

然后执行命令创建
kubectl create -f pod.yaml

## label配置文件
和pod一同创建

```
apiVersion: v1
kind: Pod
metadata: 
 name: pod-name
 namespace: dev
 labels: 
 lebelKey1: "labelValue1"
 lebelKey2: "labelValue2"
 lebelKey3: "labelValue3"
spec: 
 containers: 
    - image: nginx:1.17.1
 name: nginx-container
 ports: 
        - name: nginx-port
 containerPort: 80
 protocol: TCP
```

然后执行命令创建
kubectl create -f label.yaml

## deployment配置文件

```
apiVersion: apps/v1  # 前面的apps/必须这样写，斜杠后面随便写，暂不知为何
kind: Deployment  # 创建deployment
metadata: 
  name: ymal-deployment-name  # deployment名称
  namespace: dev  # 所属命名空间
spec: 
  replicas: 3  # 副本数量
  selector: 
    matchLabels:  # 匹配标签
      deploy-label-1: ymal-label    # 此选择器和下面的template.metadata..labels进行关联，可以发现这俩值是一样的，一样的值就会进行关联
  template:  # pod模板
    metadata:  # 以下是pod配置
      labels: 
        deploy-label-1: ymal-label
    spec: 
      containers:  # 以下是容器配置
      - image: nginx:1.17.1
        name: nginx-container
        ports: 
        - name: nginx-port
          containerPort: 80
          protocol: TCP
```

## service配置文件

```
apiVersion: v1
kind: Service
metadata: 
  name: service-name
  namespace: dev
spec: 
  clusterIP: 10.96.98.123
  ports: 
  - port: 80
    protocol: TCP
    targetPort: 80
  selector: # 标签选择器，需要和deployment的label一致
    deploy-label-1: ymal-label
  type: NodePort
```

```
apiVersion: v1
kind: Service
metadata:
  name: test-team-app-qa-vue
  namespace: test-team
  labels: 
   devtestops/app: qa-vue
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    name: http
    nodePort: 30088
  selector:
    devtestops/app: qa-vue
```
