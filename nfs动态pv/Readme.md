[TOC]

---

## 参考连接
    https://blog.csdn.net/qq_44749796/article/details/107240482

## nfs-client项目地址
    https://github.com/kubernetes-incubator/external-storage/tree/master/nfs-client
## 说明
  * NFS Client Provisioner是一个automatic provisioner，使用NFS作为存储，自动创建PV和对应的 PVC，本身不提供NFS存储，需要外部先有一套NFS存储服务。
  * PV以 `${namespace}-${pvcName}-${pvName}`的命名格式提供（在NFS服务器上）
  * PV回收的时候以 `archieved-${namespace}-${pvcName}-${pvName}` 的命名格式（在NFS 服务器上）
  * `StorageClass`属性
    * `Provisioner`（存储分配器）：用来决定使用哪个卷插件分配 PV，该字段必须指定。可以指 定内部分配器，也可以指定外部分配器。外部分配器的代码地址为： kubernetes-incubator/external-storage，其中包括NFS和Ceph等。
    * `Reclaim Policy`（回收策略）：通过`reclaimPolicy`字段指定创建的Persistent Volume的回收 策略，回收策略包括：Delete 或者 Retain，没有指定默认为Delete。
## 部署步骤
  * 拷贝上述nfs-client项目文件或目录中`nfs-client-class.yml`，`nfs-client-deploy.yaml`，`nfs-clent-rbac.yml`
  * 修改`nfs-client-deploy.yaml`中nfs NFS_SERVER地址及路径，env中`PROVISIONER_NAME`应和class中的存储分配器一致

## 测试
  * 部署`pvc-pod-example.yaml`，自动创建pv及pvc，默认删除策略Delete,删除pvc时，自动删除pv