# harbor 仓库搭建

## 通过helm仓库搭建在k8s集群
  参考：https://www.cnblogs.com/wangxu01/articles/11714308.html
```
[root@k8s-master1 harbor]# helm repo add harbor https://helm.goharbor.io
[root@k8s-master1 harbor]# helm fetch harbor/harbor
参考文件夹的变量文件，修改变量
[root@k8s-master1 harbor]# helm -n test install harbor goharbor/harbor
临时部署测试：
[root@k8s-master1 harbor]# helm -n test install harbor goharbor/harbor --set persistence.enabled=false --set expose.type=nodePort --set expose.tls.enabled=false --set externalURL=http://192.168.209.128:30002
```

# 登录及验证
  注意：需要在/etc/docker/daemon.json中添加仓库地址
``` 
[root@k8s-master1 harbor]# docker login 192.168.209.128:30002
[root@k8s-master1 harbor]# docker push  192.168.209.128:30002/library/image:tag
```