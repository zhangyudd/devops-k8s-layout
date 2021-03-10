[TOC]

# jira部署
## 部署pvc与statement应用
```
kubectl apply -f jira-pv-pvc.yaml
kubectl apply -f jira-dp-snv.yaml
```

## 等待应用启动配置web界面
语言 - 自定义设置 - 数据库连接 - 许可证（生成试用许可证，需要提前注册账号）- 傻瓜下一步 - 先看一下许可证时间
## 破解试用时间
```
kubectl cp atlassian-extras-3.2.jar jira-0:/opt/jira/atlassian-jira/WEB-INF/lib/ -n devops
先再看一下许可证时间，如果过期时间不是33年，用docker命令重启容器
```
## 验证
管理 - 版本和许可证查看许可时间