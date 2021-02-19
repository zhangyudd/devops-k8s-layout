# redis集群
## 参考链接
    https://www.cnblogs.com/Serverlessops/p/13455908.html
    git clone https://github.com/llmgo/redis-sts.git

## 部署redis集群
  1. 修改对应的namespaces，pvc，配置文件
  2. kubectl apply -f .
  3. 查看集群pod状态

## 执行集群初始化
  [root@k8s-master1]# kubectl exec -it redis-cluster-0 -- redis-cli --cluster create --cluster-replicas 1 $(kubectl get pods -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379 '| awk -F ' :' '{print $1}')

## 查看集群状态
  [root@k8s-master1]# kubectl exec -it redis-cluster-0 -- redis-cli  cluster info
## 集群连接
  pod_name.namespace_name.svc_name.svc.cluster.local:6379

# redis高可用哨兵模式
## 参考连接
  https://blog.csdn.net/jesse919/article/details/102605178
  https://www.cnblogs.com/Dev0ps/p/11259401.html

## 添加仓库并下载chart
  [root@k8s-master1]# helm repo add mirror-stable http://mirror.azure.cn/kubernetes/charts/
  [root@k8s-master1]# helm fetch mirror-stable/redis-ha --version 3.8.0 --untar --untardir ./
## 修改value.yaml
  [root@k8s-master1]# vi value.yaml

    auth
    redisPassword
    storageClass
    修改 “hardAntiAffinity: true” 为 “hardAntiAffinity: false” (仅限当replicas > worker node 节点数时修改)
## 修改redis监控及自动发现相关配置
  [root@k8s-master1]# vi value.yaml

      exporter:   # 开启监控
    enabled: true

  [root@k8s-master1]# vi templates/redis-ha-announce-service.yaml  

    annotations下添加：prometheus.io/probe: "true" 
    修改port_name：redis-exporter
## 部署
  [root@k8s-master1]# helm install redis-ha -f values.yaml ./
    
  [root@k8s-master1]# kubectl exec -it redis-ha-server-0  sh

    /data $ redis-cli
    127.0.0.1:6379> auth password

## Prometheus自动发现配置
    ## （需在svc中配置annotations: prometheus.io/probe: "true"而且port_name改为redis-exporter ）

    ######### redis 自动发现配置 ################
    - job_name: 'kube_svc_redis-exporter'
      kubernetes_sd_configs:
      - role: service
      metrics_path: /metrics
      relabel_configs:
      - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_probe]
        action: keep
        regex: true
        # 匹配svc.metadata.annotation字段包含key/value(prometheus.io/probe: "true")
      - source_labels: [__meta_kubernetes_service_port_name]
        action: keep
        regex: "redis-exporter"
        # 匹配svc.spec.ports.name 是否包含redis-exporter
      - action: labelmap
        regex: __meta_kubernetes_service_label_(.+)
      - source_labels: [__meta_kubernetes_namespace]
        target_label: kubernetes_namespace
      - source_labels: [__meta_kubernetes_service_name]
        target_label: kubernetes_name

## grafana redis图表
  模式：helm stable/redis-ha
  序号：11835
# redis单点