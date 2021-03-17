# 基于jenkins动态扩展slave方案
[Kubernetes和Jenkins的安装部署以及github的集成](https://www.yisu.com/zixun/4201.html)
[jenkins 如何在 k8s 集群中实现动态 agent](https://blog.51cto.com/wzlinux/2467307)

[kubernetes（k8s） jenkins CI/CD（动态创建slave）](https://www.jianshu.com/p/0aef1cc27d3d)




新建配置集群：
    集群名称：kubernetes
    Kubernetes 地址：https://192.168.209.128:6443
    命名空间：devops
    连接测试

    jenkins地址：http://jenkins.devops.svc.cluster.local:8080
    pods labels：键 jenkins 值 slave

    pod templates:
    名称：jnlp
    命名空间：devops
    标签列表：mmp   # jenkins中引用绑定节点
    
    container template (slave 节点创建模板)
    名称：jnlp
    docker 镜像：jenkins:slave
    工作目录：/var/jenkins_home
    运行的命令：删除留空
    命令参数：删除留空
    分配伪终端：打勾
    
    host path volume:
    主机路径：/var/run/docker.sock
    挂载路径：/var/run/docker.sock

    host path volume:
    主机路径：/usr/bin/kubectl
    挂载路径：/usr/bin/kubectl

    service account:
    jenkins

    节点选择器:
    

    工作空间卷:
    pvc

