## 一、k8s核心组件
**Kubernetes 主要由以下几个核心组件组成**：
- etcd 保存了整个集群的状态，就是一个数据库；
- apiserver 提供了资源操作的唯一入口，并提供认证、授权、访问控制、API 注册和发现等机制；
- controller manager 负责维护集群的状态，比如故障检测、自动扩展、滚动更新等；
- scheduler 负责资源的调度，按照预定的调度策略将 Pod 调度到相应的机器上；
- kubelet 负责维护容器的生命周期，同时也负责 Volume（CSI）和网络（CNI）的管理；
- Container runtime 负责镜像管理以及 Pod 和容器的真正运行（CRI）；
- kube-proxy 负责为 Service 提供 cluster 内部的服务发现和负载均衡；
**当然了除了上面的这些核心组件，还有一些推荐的插件**：
- kube-dns 负责为整个集群提供 DNS 服务
- Ingress Controller 为服务提供外网入口
- Heapster 提供资源监控
- Dashboard 提供 GUI


## 二、kubectl简介
kubectl 是 Kubernetes 的命令行工具（CLI），是 Kubernetes 用户和管理员必备的管理 工具。该kubectl工具控制Kubernetes集群管理器。它可以让您检查集群资源，创建、删除和更新组 件以及更多功能。kubectl 提供了大量的子命令，方便管理 Kubernetes 集群中的各种功能。

### 2.1.kubectl用法
kubectl -h 查看子命令列表
kubectl options 查看全局选项
kubectl --help 查看子命令的帮助
kubectl [command] [PARAMS] -o= 设置输出格式（如 json、yaml、jsonpath 等）
kubectl explain [RESOURCE] 查看资源的定义
### 2.2.kubectl 插件krew
krew 是一个用来管理 kubectl 插件的工具，类似于 apt 或 yum，支持搜索、安装和管理kubectl 插件。


## 三、安装kubectl krew
```shell script
#https://github.com/kubernetes-sigs/krew/releases/

kubectl plugin list
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/krew.tar.gz" &&
  tar zxvf krew.tar.gz &&
  KREW=./krew-"${OS}_${ARCH}" &&
  "$KREW" install krew
)    
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
```

### 3.1.安装插件及使用
```shell script
kubectl krew
kubectl krew list
kubectl krew version
kubectl krew update
kubectl krew search

kubectl krew install   ctx
kubectl krew info ctx
kubectl krew upgrade ctx

kubectl krew install   ns
kubectl krew install   get-all
kubectl krew install   minio
kubectl krew install   ingress-nginx
kubectl krew install   cert-manager
kubectl krew install   ssh-jump
```

### 3.2.常用命令
```shell script
#装完插件后可通过命令kubectl 或者kubectl- 使用插件，
# 比如'kubectl ns weave'和'kubectl-ns default'都可以切换默认表空间

# ns
kubectl ns namespaces;

# get-all
#该命令类似'kubectl get all --all-namespaces'，但更全。

# tail
kubectl-tail
kubectl-tail --ns default 
kubectl-tail --rs kubeapps-8fd98f6f5
kubectl-tail --rs kubeapps/kubeapps-8fd98f6f5 
#tail为输出pod日志，以上命令分别为：
# 输出全部pod日志、
# 输出所有命名空间default的pod日志、
# 输出全部命名空间中所有replicaset为kubeapps-8fd98f6f5的pod日志、
# 输出命名空间为kubeapps且replicaset为kubeapps-8fd98f6f5的pod日志。

# 卸载
kubectl krew uninstall tail
## rm -rf  /root/.krew 

```

### 3.3.问题
- 可能存在的问题
  - 网络原因
  ```shell script
  set -x; cd "$(mktemp -d)" &&
  wget https://github.com/Max-Qhh/Qhh-k8s/blob/main/plugin/krew%20for%20kubectl/krew.tar.gz &&
  tar zxvf krew.tar.gz &&
  ./krew-"$(uname | tr '[:upper:]' '[:lower:]')_amd64" install \
    --manifest=krew.yaml --archive=krew.tar.gz
  ```
   - sha256值
   - 本地插件包