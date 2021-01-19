### 常用命令
查看集群状态
`rabbitmqctl cluster_status`
查看用户列表
`rabbitmqctl list_users`

### 注意：如果启动后出现报错： 

=INFO REPORT==== 8-Aug-2019::08:09:41 ===

autocluster: (cleanup) No partitioned nodes found.

解决办法：需要将其他两个节点加入到第一个节点中

    #kubectl exec -it rabbitmq-xxxxx /bin/bash 

    root@rabbitmq-xxxxx:/#rabbitmqctl stop_app

    root@rabbitmq-xxxxx:/#rabbitmqctl join_cluster  rabbit@rabbitmq-0

    root@rabbitmq-xxxxx:/#rabbitmqctl start_app

若出现节点无法加入集群的问题

    root@rabbitmq-xxxxx:/#rabbitmqctl reset

    root@rabbitmq-xxxxx:/#rabbitmqctl join_cluster  rabbit@rabbitmq-0

    root@rabbitmq-xxxxx:/#rabbitmqctl start_app