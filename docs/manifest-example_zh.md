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
  kubernetesDistributions: # Define the kubernetes distribution that will be included in the artifact.
  - type: kubernetes
    version: v1.21.5
  - type: kubernetes
    version: v1.22.1
  ## The following components' versions are automatically generated based on the default configuration of KubeKey.
  components: 
    helm:
      version: v3.6.3
    cni:
      version: v0.9.1
    etcd:
      version: v3.4.13
    ## For now, if your cluster container runtime is containerd, KubeKey will add a docker 20.10.8 container runtime in the below list.
    ## The reason is KubeKey creates a cluster with containerd by installing a docker first and making kubelet connect the socket file of containerd which docker contained.
    containerRuntimes:
    - type: docker
      version: 20.10.8
    crictl:
      version: v1.22.0
    ## The following components define the private registry files that will be included in the artifact.
    docker-registry:
      version: "2"
    harbor:
      version: v2.4.1
    docker-compose:
      version: v2.2.2
  ## Define the images that will be included in the artifact.
  ## When you generate this file using KubeKey, all the images contained on the cluster hosts will be automatically added. 
  ## Of course, you can also modify this list of images manually.
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
  ## Define the authentication information if you need to pull images from a registry that requires authorization.
  registry:
    auths:
      "dockerhub.kubekey.local":
        username: "xxx"
        password: "***"
        skipTLSVerify: false # Allow contacting registries over HTTPS with failed TLS verification.
        plainHTTP: false # Allow contacting registries over HTTP.
        certsPath: "/etc/docker/certs.d/dockerhub.kubekey.local" # Use certificates at path (*.crt, *.cert, *.key) to connect to the registry.
```
