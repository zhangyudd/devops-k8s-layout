[TOC]
# 自动申请ssl证书

## Create the namespace for cert-manager
[yuadmin@k8s-master1 ~]$ kubectl create namespace cert-manager
 
## Add the Jetstack Helm repository
[yuadmin@k8s-master1 ~]$ helm repo add jetstack https://charts.jetstack.io
 
## Update your local Helm chart repository cache
[yuadmin@k8s-master1 ~]$ helm repo update
 
## Install the cert-manager Helm chart
[yuadmin@k8s-master1 ~]$ helm install cert-manager \
  --namespace cert-manager \
  --version v0.13.1 \
  jetstack/cert-manager
 
## 检查是否安装成功
[yuadmin@k8s-master1 ~]$ kubectl get pods --namespace cert-manager

## 需要创建得命名空间
[yuadmin@k8s-master1 ~]$ kubectl apply -f production-issuer.yaml 
```
apiVersion: cert-manager.io/v1alpha2
kind: Issuer
metadata:
  name: letsencrypt-prod #这里是issuer的名称，后面要使用
  namespace: devops
spec:
  acme:
    # 邮箱，证书过期前会发邮件到这个邮箱
    email: edgar.z@foxmail.com
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: nginx
```

## 添加ingress申请域名证书
```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: kuard
  annotations:
    kubernetes.io/ingress.class: "nginx"    # letsencrypt
    cert-manager.io/issuer: "letsencrypt-prod" # letsencrypt
    nginx.ingress.kubernetes.io/force-ssl-redirect: 'true'  # 强制跳转https
```