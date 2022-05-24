# argo
Argo Workflows 是一个开源的容器原生的工作流引擎，可在 Kubernetes 上编排并行作业。Argo Workflows 实现为 Kubernetes CRD。

<!-- GFM-TOC -->
* [argo](#argo)
    * [参考](#参考)
    * [安装控制器端](#安装控制器端)
    * [安装Client端](#安装client端)
<!-- GFM-TOC -->

## 参考
[Kubernetes 原生 CI/CD 构建框架 Argo 详解！](http://www.360doc.com/content/21/0130/15/46368139_959729495.shtml)

[Argo Workflows-Kubernetes的工作流引擎](https://blog.csdn.net/wanger5354/article/details/122422713)

## 安装控制器端

```
kubectl create ns argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/stable/manifests/quick-start-postgres.yaml
```

## 安装Client端

```
# Download the binary
curl -sLO https://github.com/argoproj/argo/releases/download/v3.0.0-rc4/argo-linux-amd64.gz

# Unzip
gunzip argo-linux-amd64.gz

# Make binary executable
chmod +x argo-linux-amd64

# Move binary to path
mv ./argo-linux-amd64 /usr/local/bin/argo
```

这一步没有实操，因为argo已经在机器安装好了

```
# 查看argo版本
argo version
```





