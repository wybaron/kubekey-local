```yaml
apiVersion: kubekey.kubesphere.io/v1alpha2
kind: Cluster
metadata:
  name: sample
spec:
  hosts:
  # 假设SSH的默认端口是22，否则在IP地址后面加上端口号。 
  # If you install Kubernetes on ARM, add "arch: arm64". For example, {...user: ubuntu, password: Qcloud@123, arch: arm64}.
  - {name: node1, address: 172.16.0.2, internalAddress: 172.16.0.2, port: 8022, user: ubuntu, password: "Qcloud@123"}
  # For default root user.
  # Kubekey 将解析 labels 字段并自动标记节点。
  - {name: node2, address: 172.16.0.3, internalAddress: 172.16.0.3, password: "Qcloud@123", labels: {disk: SSD, role: backend}}
  # 使用 SSH 密钥进行无密码登录。
  - {name: node3, address: 172.16.0.4, internalAddress: 172.16.0.4, privateKeyPath: "~/.ssh/id_rsa"}
  roleGroups:
    etcd:
    - node1 # 集群中作为 etcd 节点的所有节点。
    master:
    - node1
    - node[2:10] # From node2 to node10. All the nodes in your cluster that serve as the master nodes.
    worker:
    - node1
    - node[10:100] # All the nodes in your cluster that serve as the worker nodes.
  controlPlaneEndpoint:
    #apiservers 的内部负载均衡器。支持：haproxy、kube-vip [Default: ""]
    internalLoadbalancer: haproxy 
    domain: lb.kubesphere.local
    # 您的负载均衡器的 IP 地址。如果您在“kube-vip”模式下使用 internalLoadblancer，则此处需要一个 VIP。
    address: ""      
    port: 6443
  system:
    # chrony 的 ntp 服务器。
    ntpServers:
      - time1.cloud.tencent.com
      - ntp.aliyun.com
      - node1 # 如果没有公共 ntp 服务器访问，请将 hosts 中的节点名称设置为 ntp 服务器。
    timezone: "Asia/Shanghai"
    # Specify additional packages to be installed. The ISO file which is contained in the artifact is required.
    rpms:
      - nfs-utils
    # Specify additional packages to be installed. The ISO file which is contained in the artifact is required.
    debs: 
      - nfs-common
    #preInstall:  # 为每个节点指定自定义的init shell脚本，并按照列表顺序执行。
    #  - name: format and mount disk  
    #    bash: /bin/bash -x setup-disk.sh
    #    materials: # scripts can has some dependency materials. those will copy to the node        
    #      - ./setup-disk.sh # the script which shell execute need
    #      -  xxx            # other tools materials need by this script
    #postInstall: # 在 kubernetes 安装后为每个节点指定自定义完成清理 shell 脚本。
    #  - name: clean tmps files
    #    bash: |
    #       rm -fr /tmp/kubekey/*
    #skipConfigureOS: true # 不要预先配置主机操作系统（例如内核模块、/etc/hosts、sysctl.conf、NTP 服务器等）。在使用 KubeKey 之前，您必须通过其他方法设置这些东西。
  kubernetes:
    version: v1.21.5
    # Optional extra Subject Alternative Names (SANs) to use for the API Server serving certificate. Can be both IP addresses and DNS names.
    apiserverCertExtraSans:  
      - 192.168.8.8
      - lb.kubespheredev.local
    #容器运行时，支持： containerd, cri-o, isula. [Default: docker]
    containerManager: docker
    clusterName: cluster.local
    # 是否安装自动更新 Kubernetes 控制平面证书的脚本. [Default: false]
    autoRenewCerts: true
    # masqueradeAll tells kube-proxy to SNAT everything if using the pure iptables proxy mode. [Default: false].
    masqueradeAll: false
    # maxPods is the number of Pods that can run on this Kubelet. [Default: 110]
    maxPods: 110
    # podPidsLimit is the maximum number of PIDs in any pod. [Default: 10000]
    podPidsLimit: 10000
    # The internal network node size allocation. This is the size allocated to each node on your network. [Default: 24]
    nodeCidrMaskSize: 24
    # 指定要使用的代理模式。 [Default: ipvs]
    proxyMode: ipvs
    # enable featureGates, [Default: {"ExpandCSIVolumes":true,"RotateKubeletServerCertificate": true,"CSIStorageCapacity":true, "TTLAfterFinished":true}]
    featureGates: 
      CSIStorageCapacity: true
      ExpandCSIVolumes: true
      RotateKubeletServerCertificate: true
      TTLAfterFinished: true
    ## support kata and NFD
    # kata:
    #   enabled: true
    # nodeFeatureDiscovery
    #   enabled: true
    # additional kube-proxy configurations
    kubeProxyConfiguration:
      ipvs:
        # CIDR's to exclude when cleaning up IPVS rules.
        # necessary to put node cidr here when internalLoadbalancer=kube-vip and proxyMode=ipvs
        # refer to: https://github.com/kubesphere/kubekey/issues/1702
        excludeCIDRs:
          - 172.16.0.2/24
  etcd:
    # 指定集群使用的 etcd 类型。当集群类型为k3s时，设置该参数为kubeadm无效。 [kubekey | kubeadm | external] [Default: kubekey]
    type: kubekey  
    ## 以下参数仅在类型设置为外部（external）时才需要添加。
    ## caFile, certFile and keyFile need not be set, if TLS authentication is not enabled for the existing etcd.
    # external:
    #   endpoints:
    #     - https://192.168.6.6:2379
    #   caFile: /pki/etcd/ca.crt
    #   certFile: /pki/etcd/etcd.crt
    #   keyFile: /pki/etcd/etcd.key
    dataDir: "/var/lib/etcd"
    # 心跳间隔的时间（以毫秒为单位）。
    heartbeatInterval: "250"
    # 选举超时的时间（以毫秒为单位）。
    electionTimeout: "5000"
    # 触发快照到磁盘的已提交事务数。
    snapshotCount: "10000"
    # 以小时为单位的 mvcc 键值存储的自动压缩保留。 0 表示禁用自动压缩。
    autoCompactionRetention: "8"
    # 设置 etcd 导出指标的详细程度，指定 'extensive' 以包括直方图指标。
    metrics: basic
    ## Etcd 的空间配额默认为 2G。如果您在 etcd_memory_limit 中放置一个小于 etcd_quota_backend_bytes 的值，您可能会遇到 etcd 集群的内存不足终止。请查看 etcd 文档以获取更多信息。
    # 8G 是正常环境的建议最大大小，如果配置值超过它，etcd 会在启动时发出警告。
    quotaBackendBytes: "2147483648" 
    # 服务器将接受的最大客户端请求大小（以字节为单位）。
    #  etcd 旨在处理典型的元数据小键值对。
    # 较大的请求可以工作，但可能会增加其他请求的延迟
    maxRequestBytes: "1572864"
    # Maximum number of snapshot files to retain (0 is unlimited)
    maxSnapshots: 5
    # Maximum number of wal files to retain (0 is unlimited)
    maxWals: 5
    # Configures log level. Only supports debug, info, warn, error, panic, or fatal.
    logLevel: info
  network:
    plugin: calico
    calico:
      ipipMode: Always  # IPIP Mode to use for the IPv4 POOL created at start up. If set to a value other than Never, vxlanMode should be set to "Never". [Always | CrossSubnet | Never] [Default: Always]
      vxlanMode: Never  # VXLAN Mode to use for the IPv4 POOL created at start up. If set to a value other than Never, ipipMode should be set to "Never". [Always | CrossSubnet | Never] [Default: Never]
      vethMTU: 0  # The maximum transmission unit (MTU) setting determines the largest packet size that can be transmitted through your network. By default, MTU is auto-detected. [Default: 0]
    kubePodsCIDR: 10.233.64.0/18
    kubeServiceCIDR: 10.233.0.0/18
  storage:
    openebs:
      basePath: /var/openebs/local # base path of the local PV provisioner
  registry:
    registryMirrors: []
    insecureRegistries: []
    privateRegistry: ""
    namespaceOverride: ""
    auths: # if docker add by `docker login`, if containerd append to `/etc/containerd/config.toml`
      "dockerhub.kubekey.local":
        username: "xxx"
        password: "***"
        skipTLSVerify: false # Allow contacting registries over HTTPS with failed TLS verification.
        plainHTTP: false # Allow contacting registries over HTTP.
        certsPath: "/etc/docker/certs.d/dockerhub.kubekey.local" # Use certificates at path (*.crt, *.cert, *.key) to connect to the registry.
  addons: [] # You can install cloud-native addons (Chart or YAML) by using this field.

```
