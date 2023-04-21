# 快速开始
以下是 kubernetes v1.21.5 集群的清单文件示例。它包含 `ubuntu 20.04` 和 `centos 7` 的存储库，一些必要的组件，私有registry，和必要的images。
```yaml
apiVersion: kubekey.kubesphere.io/v1alpha2
kind: Manifest
metadata:
  name: sample
spec:
  arches: 
  - amd64
  operatingSystems: 
  - arch: amd64
    type: linux
    id: ubuntu
    version: "20.04"
    osImage: Ubuntu 20.04.3 LTS
    repository: 
      iso:
        localPath: 
        url: https://github.com/kubesphere/kubekey/releases/download/v2.0.0/ubuntu-20.04-amd64-debs.iso
  - arch: amd64
    type: linux
    id: centos
    version: "7"
    osImage: CentOS Linux 7 (Core)
    repository:
      iso:
        localPath:
        url: https://github.com/kubesphere/kubekey/releases/download/v2.0.0/centos-7-amd64-rpms.iso
  kubernetesDistributions: 
  - type: kubernetes
    version: v1.21.5
  components: 
    helm:
      version: v3.6.3
    cni:
      version: v0.9.1
    etcd:
      version: v3.4.13
    containerRuntimes:
    - type: docker
      version: 20.10.8
    crictl:
      version: v1.22.0
    docker-registry:
      version: "2"
    harbor:
      version: v2.4.1
    docker-compose:
      version: v2.2.2
  images:
  - docker.io/calico/cni:v3.20.0
  - docker.io/calico/kube-controllers:v3.20.0
  - docker.io/calico/node:v3.20.0
  - docker.io/calico/pod2daemon-flexvol:v3.20.0
  - docker.io/coredns/coredns:1.8.0
  - docker.io/kubesphere/k8s-dns-node-cache:1.15.12
  - docker.io/kubesphere/kube-apiserver:v1.21.5
  - docker.io/kubesphere/kube-controller-manager:v1.21.5
  - docker.io/kubesphere/kube-proxy:v1.21.5
  - docker.io/kubesphere/kube-scheduler:v1.21.5
  - docker.io/kubesphere/pause:3.4.1
```

# 清单定义
以下是清单文件的完整字段定义。
```yaml
apiVersion: kubekey.kubesphere.io/v1alpha2
kind: Manifest
metadata:
  name: sample
spec:
  arches: # 定义将包含在artifact中的架构。
  - arm64
  operatingSystems: # 定义将包含在artifact中的操作系统。
  - arch: amd64
    type: linux
    id: ubuntu
    version: "20.04"
    osImage: Ubuntu 20.04.3 LTS
    repository: # 定义将包含在artifact中的操作系统存储库 iso 文件。
      iso:
        localPath: ./ubuntu.iso # 定义从本地路径获取 iso 文件。
        url: # 定义从 URL 获取 iso 文件。
  - arch: amd64
    type: linux
    id: centos
    version: "7"
    osImage: CentOS Linux 7 (Core)
    repository:
      iso:
        localPath:
        url: https://github.com/kubesphere/kubekey/releases/download/v2.0.0/centos-7-amd64-rpms.iso
  kubernetesDistributions: # 定义将包含在artifact中的 kubernetes 发行版。
  - type: kubernetes
    version: v1.21.5
  - type: kubernetes
    version: v1.22.1
  ## 以下组件的版本是根据 KubeKey 的默认配置自动生成的。
  components: 
    helm:
      version: v3.6.3
    cni:
      version: v0.9.1
    etcd:
      version: v3.4.13
    ## 如果您当前的集群容器运行时是 containerd，KubeKey 将在下面的列表中添加一个 docker 20.10.8 容器运行时。
    ## 原因是 KubeKey 通过先安装一个 docker 并让 kubelet 连接 docker 包含的 containerd 的套接字文件来创建一个带有 containerd 的集群。
    containerRuntimes:
    - type: docker
      version: 20.10.8
    crictl:
      version: v1.22.0
    ## 以下组件定义了将包含在artifact中的私有registry。
    docker-registry:
      version: "2"
    harbor:
      version: v2.4.1
    docker-compose:
      version: v2.2.2
  ## 定义将包含在artifact中的images。
  ## 当您使用 KubeKey 生成此文件时，将自动添加集群主机上包含的所有images。
  ## 当然，你也可以手动修改这个images列表。
  images:
  - docker.io/calico/cni:v3.20.0
  - docker.io/calico/kube-controllers:v3.20.0
  - docker.io/calico/node:v3.20.0
  - docker.io/calico/pod2daemon-flexvol:v3.20.0
  - docker.io/coredns/coredns:1.8.0
  - docker.io/kubesphere/k8s-dns-node-cache:1.15.12
  - docker.io/kubesphere/kube-apiserver:v1.21.5
  - docker.io/kubesphere/kube-controller-manager:v1.21.5
  - docker.io/kubesphere/kube-proxy:v1.21.5
  - docker.io/kubesphere/kube-scheduler:v1.21.5
  - docker.io/kubesphere/pause:3.4.1
  - dockerhub.kubekey.local/kubesphere/kube-apiserver:v1.22.1
  - dockerhub.kubekey.local/kubesphere/kube-controller-manager:v1.22.1
  - dockerhub.kubekey.local/kubesphere/kube-proxy:v1.22.1
  - dockerhub.kubekey.local/kubesphere/kube-scheduler:v1.22.1
  - dockerhub.kubekey.local/kubesphere/pause:3.5
  ## 如果您需要从需要授权的registry中拉取images，请定义身份验证信息。
  registry:
    auths:
      "dockerhub.kubekey.local":
        username: "xxx"
        password: "***"
        skipTLSVerify: false # 允许在 TLS 验证失败的情况下通过 HTTPS 连接registries。
        plainHTTP: false # 允许通过 HTTP 连接 registries.
        certsPath: "/etc/docker/certs.d/dockerhub.kubekey.local" # Use certificates at path (*.crt, *.cert, *.key) to connect to the registry.
```
