# redis集群
## 参考链接
    https://www.cnblogs.com/Serverlessops/p/13455908.html
    git clone https://github.com/llmgo/redis-sts.git

## 部署redis集群
  1. 修改对应的namespaces，pvc，配置文件
  2. kubectl apply -f .
  3. 查看集群pod状态

## 执行集群初始化
    kubectl exec -it redis-cluster-0 -- redis-cli --cluster create --cluster-replicas 1 $(kubectl get pods -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379 '| awk -F ' :' '{print $1}')

## 查看集群状态
    kubectl exec -it redis-cluster-0 -- redis-cli  cluster info
## 集群连接
    pod_name.namespace_name.svc_name.svc.cluster.local:6379

# redis高可用哨兵模式
## 参考连接
    https://blog.csdn.net/jesse919/article/details/102605178
    https://www.cnblogs.com/Dev0ps/p/11259401.html

    helm repo add stable http://mirror.azure.cn/kubernetes/charts/
    helm fetch stable/redis-ha --untar --untardir ./


# redis单点