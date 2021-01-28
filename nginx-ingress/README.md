[TOC]

---


## 项目地址
[传送门](https://github.com/kubernetes/ingress-nginx/tree/nginx-0.30.0 "nignx-ingress项目地址")
## 部署步骤
下方README.md
> See /docs/deploy.
>> ingress-nginx/docs/deploy/index.md
---
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.30.0/deploy/static/mandatory.yaml

kubectl apply -f ingress-service.yaml

如需要修改nginx-ingress配置参数
`nginx-config.yaml ` 创建配置文件，配置全局生效

---
`nginx-deploy.yaml` 部署nginx应用，包含deployment，service，ingress

annotations配置可以针对某个应用单独配置
* 保持session
  * 部署文件中配置
    ``` 
    annotations:
    nginx.ingress.kubernetes.io/affinity: cookie
    nginx.ingress.kubernetes.io/session-cookie-hash: sha1
    nginx.ingress.kubernetes.io/session-cookie-name: route
    ```
---
`tcp-sv-config.yaml` 转发tcp端口应用
`tcp-kuard-dome.yaml` tcp应用实例
注意：需要在ingress-service中开启对应的端口

---
## Ingress nginx常用规则
### 声明入口
```
containers:
- name: nginx-ingress-controller
  image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.24.1
  args:
    - /nginx-ingress-controller
    - --configmap=$(POD_NAMESPACE)/nginx-configuration
    - --tcp-services-configmap=$(POD_NAMESPACE)/tcp-services
    - --udp-services-configmap=$(POD_NAMESPACE)/udp-services
    - --publish-service=$(POD_NAMESPACE)/ingress-nginx
    - --annotations-prefix=nginx.ingress.kubernetes.io
    - --ingress-class=nginx # 指定ingress-class 属性
```
`--ingress-class`：声明ingress入口名称，如果要绑定这个ingress，需要在 annotation 中定义 `kubernetes.io/ingress.class: "nginx"`
### 开启TLS
`创建ssl证书`
```$ kubectl create secret tls www-example-com --key tls.key --cert tls.crt -n default```

`nginx.ingress.kubernetes.io/ssl-redirect` 默认为 `true`，启用 TLS 时，http请求会 308 重定向到https

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: "nginx" # 绑定ingress-class
    nginx.ingress.kubernetes.io/ssl-redirect: "false" # 关闭SSL跳转
spec:
  rules:
  - host: www.example.com
    http:
      paths:
      - path: /
        backend:
          serviceName: demo-svc
          servicePort: 8080
  tls:
  - secretName: www-example-com
    hosts:
    - www.example.com
```
### 配置白名单IP范围
```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: "nginx" # 绑定ingress-class
    nginx.ingress.kubernetes.io/whitelist-source-range: 10.0.0.0/24,172.10.0.1
spec:
  rules:
  - host: www.example.com
    http:
      paths:
      - path: /
        backend:
          serviceName: demo-svc
          servicePort: 8080
```
### rewrite 配置
`下面 rewrite 规则意思是 访问 www.example.com/hello/(.*) 跳转到 www.example.com/(.*)`
```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: "nginx" # 绑定ingress-class
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: "/$1"
spec:
  rules:
  - host: www.example.com
    http:
      paths:
      - path: /hello/(.*)$
        backend:
          serviceName: demo-svc
          servicePort: 8080
```
### 限速
`设置 www.example.com/login 登陆页为每秒100个连接数，10.0.0.0/24,172.10.0.1 IP段不在限速范围`
```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: "nginx"  # 绑定ingress-class
    nginx.ingress.kubernetes.io/limit-rps: '100'  # 限制客户端每秒传输的字节数
    nginx.ingress.kubernetes.io/limit-whitelist: 10.0.0.0/24,172.10.0.1  # 白名单中的IP不限速
    nginx.ingress.kubernetes.io/limit-rate: 100K  # 单个IP每秒的连接数
    nginx.ingress.kubernetes.io/limit-rpm: 30  # 单个IP每分钟的连接数 
spec:
  rules:
  - host: www.example.com
    http:
      paths:
      - path: /login
        backend:
          serviceName: demo-svc
          servicePort: 8080
```
